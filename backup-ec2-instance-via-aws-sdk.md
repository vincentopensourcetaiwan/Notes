# references:

[AWS SDK for Ruby](http://aws.amazon.com/sdkforruby/ "AWS SDK for Ruby")

[Getting Started with the AWS SDK for Ruby](http://aws.amazon.com/developers/getting-started/ruby/ "Getting Started with the AWS SDK for Ruby")

[API Documentation](http://docs.aws.amazon.com/AWSRubySDK/latest/frames.html "API Documentation")

**Gemfile**

    gem 'aws-sdk'

**/ lib / tasks / backup_ec2.rake**

    aws_region = 'ap-northeast-1'
    aws_access_key_id = 'your aws access key id'
    aws_secret_access_key = 'your aws secret access key'
    aws_instance_id = 'your aws instance_id'
    aws_owner_id = 'your aws owner_id'
    image_name = "backup-brandinlans-#{Time.now.year}-#{Time.now.month}-#{Time.now.day}-#{Time.now.hour}-#{Time.now.min}"
    image_description= image_name

    namespace :ec2 do
      desc 'backup ec2 ami'
      task :backup_ec2_ami => :environment do
        ec2 = AWS::EC2.new(:access_key_id => aws_access_key_id, :secret_access_key => aws_secret_access_key, :region => aws_region)
        instance = ec2.instances[aws_instance_id]
        instance.create_image(image_name, :description => image_description, :no_reboot => true)
        puts image_name
      end

      desc 'remove old ec2 ami backup after 7 days'
      task :remove_old_ec2_ami_backup_after_7_days => :environment do
        ec2 = AWS::EC2.new(:access_key_id => aws_access_key_id, :secret_access_key => aws_secret_access_key, :region => aws_region)
        images = ec2.images.with_owner(aws_owner_id)
        snapshots = ec2.snapshots.with_owner(aws_owner_id)

        images.each do |image|
          unless image.name.nil?
            split_name = image.name.split('-')
            year = split_name[2]
            month = split_name[3]
            day = split_name[4]
            date = Time.new(year, month, day)
            if date < Time.now - 7.days
              image.deregister
              puts "deregister #{name}"

              snapshots.each do |snapshot|
                if snapshot.description.include? image.image_id
                  snapshot.delete
                  puts "delete snapshot #{snapshot.description}"
                end
              end
            end
          end
        end
      end
    end

**/ config / schedule.rb**

    every :day, :at => '3:00am' do
      rake 'ec2:backup_ec2_ami'
      rake 'ec2:remove_old_ec2_ami_backup_after_7_days'
    end


