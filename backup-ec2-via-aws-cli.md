# environment

    Ubuntu 12.04

# references:

[Getting Set Up with the AWS Command Line Interface](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html "Getting Set Up with the AWS Command Line Interface")

[aws / aws-cli](https://github.com/aws/aws-cli "aws / aws-cli")

[how do i enable the multiverse repository](http://askubuntu.com/questions/89096/how-do-i-enable-the-multiverse-repository "how do i enable the multiverse repository")

[Environment Variables](https://help.ubuntu.com/community/EnvironmentVariables "Environment Variables")

[aws security credential](https://console.aws.amazon.com/iam/home?#security_credential "aws security credential")

[Regions and Endpoints](http://docs.aws.amazon.com/general/latest/gr/rande.html#ec2_region "Regions and Endpoints")

[ec2-create-image](http://docs.aws.amazon.com/AWSEC2/latest/CommandLineReference/ApiReference-cmd-CreateImage.html "ec2-create-image")

[javan / whenever](https://github.com/javan/whenever "javan / whenever")

# install aws cli

to see if you have Python installed, type the following at a command prompt:

    python --version

if you have easy_install (check by running easy_install --help)

    easy_install --help

install easy_install

    sudo apt-get install python-setuptools

if you have easy_install (check by running easy_install --help)

    easy_install --help

install the AWS CLI

    sudo easy_install awscli

upgrade the AWS CLI

    sudo easy_install --upgrade awscli

to check if have aws cli installed

    aws help


# install aws ec2 tool

enable the “multiverse” repository

    sudo vi /etc/apt/sources.list

uncomment (remove the # from the front of) the multiverse lines or add them in if needed, so the lines look like this:

    ## N.B. software from this repository is ENTIRELY UNSUPPORTED by the Ubuntu
    ## team, and may not be under a free licence. Please satisfy yourself as to
    ## your rights to use the software. Also, please note that software in
    ## multiverse WILL NOT receive any review or updates from the Ubuntu
    ## security team.
    deb http://us.archive.ubuntu.com/ubuntu/ oneiric multiverse
    #deb-src http://us.archive.ubuntu.com/ubuntu/ oneiric multiverse
    deb http://us.archive.ubuntu.com/ubuntu/ oneiric-updates multiverse
    #deb-src http://us.archive.ubuntu.com/ubuntu/ oneiric-updates multiverse

update repository

    sudo apt-get update

install aws ec2 tool

    sudo apt-get install ec2-api-tools

# setup automation for backup ami image

## use whenever gem

add aws security credential to environment variable

    sudo vi /home/deployer/.profile

add following codes into the file

    export AWS_ACCESS_KEY_ID=xxxxxxxxxxxxxxxxxxxx
    export AWS_SECRET_ACCESS_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    export AWS_DEFAULT_REGION=ap-northeast-1

config/schedule.rb

    every 1.days do
        command "aws ec2 create-image --instance-id i-xxxxxxxx --name backup-brandinlabs-#{Time.now.year}-#{Time.now.month}-#{Time.now.day} --no-reboot"
    end



