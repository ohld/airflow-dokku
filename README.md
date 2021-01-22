# airflow-dokku
My attempt to run Airflow with Dokku PaaS. In production-ready mode (with Postgres, Redis). This code should also work with Heroku (so this can be called `airflow-heroku`) but I have not tested it.

## Local testing

Init everything first of all
```
export AIRFLOW_HOME=$(pwd)
airflow upgrade
```

Create super user
``` bash
airflow users create \
    --username admin \
    --firstname Peter \
    --lastname Parker \
    --role Admin \
    --email spiderman@superhero.org
```

Run webserver and scheduler (in another terminal)
```
airflow webserver --port 8080
airflow scheduler
```

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


---- 

[Contact me](https://twitter.com/danokhlopkov) if you have some questions. 