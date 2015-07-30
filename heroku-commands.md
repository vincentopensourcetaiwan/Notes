### Managing Multiple Environments for an App

[Managing Multiple Environments for an App](https://devcenter.heroku.com/articles/multiple-environments)

apply the operation to production environment

    heroku create --remote production
    git push production master
    heroku run rake db:migrate --remote production
    heroku logs -t --remote production
    heroku ps --remote production

apply the operation to staging environment

    heroku create --remote staging
    git push staging master
    heroku run rake db:migrate --remote staging
    heroku logs -t --remote staging
    heroku ps --remote staging

set “staging” to be the default remote

    git config heroku.remote staging


### Renaming Apps from the CLI

[Renaming Apps from the CLI](https://devcenter.heroku.com/articles/renaming-apps)

    heroku apps:rename newname --remote staging

### reset database

[Running Rake Commands](https://devcenter.heroku.com/articles/rake)

[How to empty DB in heroku](http://stackoverflow.com/questions/4820549/how-to-empty-db-in-heroku)

    heroku pg:reset HEROKU_POSTGRESQL_OLIVE_URL --confirm vocabulary-staging

    heroku run rake db:migrate --remote staging

    heroku run rake db:seed --remote staging

### start and restart app

[start and restart app](https://devcenter.heroku.com/articles/getting-started-with-rails3)

    heroku restart --remote staging

    heroku open --remote staging

### Configuration and Config Vars

[Configuration and Config Vars](https://devcenter.heroku.com/articles/config-vars)

list all environment variables

    heroku config --remote staging

set up environment variables

    heroku config:set host=vocabulary-staging.herokuapp.com --remote staging


### PG Backups

[PG Backups](https://devcenter.heroku.com/articles/pgbackups)

Creating a backup

    heroku pgbackups:capture --expire --remote staging

List backups

    heroku pgbackups --remote staging












