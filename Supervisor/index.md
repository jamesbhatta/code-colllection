### Install supervisor
```
sudo apt-get install supervisor
```


To verify the status of the Supervisor service with the following command:
```
systemctl status supervisor
```

You should get the following output:
```
supervisor.service - Supervisor process control system for UNIX
Loaded: loaded (/lib/systemd/system/supervisor.service; enabled; vendor preset: enabled)
Active: active (running) since Thu 2020-10-01 13:31:42 UTC; 9s ago
Docs: http://supervisord.org
Main PID: 14419 (supervisord)
Tasks: 1 (limit: 2353)
Memory: 14.2M
CGroup: /system.slice/supervisor.service
└─14419 /usr/bin/python3 /usr/bin/supervisord -n -c /etc/supervisor/supervisord.conf
```

### Configure a new worker
create a new configuation in  ``/etc/supervisor/conf.d`` Ex: ``touch /etc/supervisor/conf.d/laravel-worker.conf`` with the following:

```
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /var/www/app.com/artisan queue:work sqs --sleep=3 --tries=3 --max-time=3600
autostart=true
autorestart=true
stopasgroup=true
killasgroup=true
user=root
numprocs=8
redirect_stderr=true
stdout_logfile=/var/www/app.com/storage/logs/worker.log
stopwaitsecs=3600
```

### Starting Supervisor
Once the configuration file has been created, you may update the Supervisor configuration and start the processes using the following commands:

```
sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl start laravel-worker:*
```
