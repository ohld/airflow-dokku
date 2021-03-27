# airflow-dokku
My attempt to run Airflow with Dokku PaaS. In production-ready mode (with Postgres, Redis). This code should also work with Heroku (so this can be called `airflow-heroku`) but I have not tested it.

## Production

I really love to book a small droplet on Digital Ocean, install there a Dokku instance and then to try to run everything as Dokku apps.

### Create Dokku app for Airflow

``` bash
dokku apps:create airflow
```

### Plugins for production-ready Airflow

You need to install & attach Postgres and Redis (this is my random choice, you can try to replace these to Mysql & RabbitMQ).

``` bash
dokku postgres:create airflow
dokku postgres:link airflow airflow --alias "AIRFLOW__CORE__SQL_ALCHEMY_CONN"
```

I don't need to scale Airflow's Celery now so I'll skip the Redis installation and attachment (but it is almost the same as for Postgres).


### Setup the env variables

``` bash
dokku config:set airflow \
AIRFLOW__CORE__EXECUTOR=LocalExecutor \
AIRFLOW__CORE__FERNET_KEY='<any large random secret string>' \
AIRFLOW__CORE__LOAD_EXAMPLES=False \
AIRFLOW__CORE__SQL_ALCHEMY_CONN=<copy of DATABASE_URL value from Postgres>
```


### Create Airflow superuser

Open Dokku app's shell:

``` bash 
dokku enter airflow web
```

And then call the proper airflow CLI command:
``` bash
airflow users create \
    --username admin \
    --firstname Peter \
    --lastname Parker \
    --role Admin \
    --email spiderman@superhero.org
```


## Activate Dokku auto deploys with Github Actions CI

Open the file at `.github/workflows/dokku.yml` and add there the URL to the machine where your Dokku runs. Then open Github repo's Settings tab -> Secrets and add `SHH_PRIVATE_KEY` variable. GitHub Action CI will use this key to connect to your machine via ssh.

## HTTPs with Letsencrypt
If you attach a domain to a Dokku app you can add a HTTPs security to your service. This can be done with [Dokku Letsencrypt](https://github.com/dokku/dokku-letsencrypt) plugin.

```bash
dokku letsencrypt:enable airflow
```

---- 

[Contact me](https://twitter.com/danokhlopkov) if you have any questions. 