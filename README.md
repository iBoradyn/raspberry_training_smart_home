# Raspberry training smart home
Project with endpoints, webhooks and ready views using them for controlling electrical devices via raspberry pi.


## Applications
### Watering system
Application managing a plant watering system. It allows to manually start and stop automatic watering and create a schedule.
#### Endpoints
- ```/watering_system/turn-on/```
- ```/watering_system/turn-off/```
- ```/watering_system/status/```
#### Webhooks
- ```ws/pump_status/```

### Door opener
An application that controls the rotation of the mini-motor that opens and closes the door latch.
#### Endpoints
- ```/door_opener/open-door/```
- ```/door_opener/close-door/```
- ```/door_opener/status/```
#### Webhooks
- ```ws/door_status/```


## Required services
- PostrgeSQL
- Redis
- Gunicorn
- Daphne
- Celery
- Celery Beat


## Local project configuration
- Create postgresql database
- Create .env file based on .env.example
- `pip install -r requirements.txt` or `pip install -r requirements-dev.txt` for local development
- `python manage.py migrate`
- `python manage.py createsuperuser`
- `python manage.py collectstatic --noinput`
- Start redis on default port `6379`
  - `redis-server`
- Start Gunicorn on default port `8000`
  - `gunicorn --workers 3 project.wsgi:application`
- Start Daphne on port `8001`
  - `daphne -b 0.0.0.0 -p 8001 project.asgi:application`
- Start Celery worker
  - `celery -A project worker -l INFO`
- Start Celery Beat
  - `celery -A project beat -l INFO`
- Login to admin panel to create pump and motor objects and set timings.


## Raspberry Pi GPIO pins
Pin usage in live setup.

#### Requirements
- Raspberry Pi 4B
- Relay modules
  - one for watering system
  - two for door opener

#### Watering system requirements
- Watering pump for watering system
- External power source ```(e.g. charger with volts matching pumps volts)```

#### Door opener requirements
- Mini motor for door opener
- External power source ```(e.g. charger/battery with volts matching motor volts)```

### Watering system
1. Connect `GPIO15` pin with relay module `IN` pin.
2. Connect ground wire from external source to ground on pump. 
3. Connect relay `NO output` to power on pump.
4. Connect power wire from external source to `COM output`.

### Door opener
1. Connect `GPIO17` to first relay(`R1`) IN pin and `GPIO22` to second relay(`R2`) `IN` pin.
2. Split ground wire from external source into two ends and connect them to `R1` and `R2` `NC outputs`. 
3. Connect `R1` `NO output` to power on motor.
4. Connect `R2` `NO output` to ground on motor.
5. Split power wire from external source into two ends and connect them to `R1` and `R2` `COM outputs`. 

## SASS File Watcher config (PyCharm)

Most scss files are imported to `base.scss` which is compiled into `base.css` in css directory.
If the file is not imported into `base.scss`, it will be compiled as a separate file with the same name in the css directory. 
> NOTE: The files are created directly in the `css` directory even if the source files are in a subdirectory.

1. File > Settings > Tools > File Watchers > Add('+') > SCSS
2. Scope: '...' > Add scope('+') > Local
   - Select scss directory and click 'Include Recursively'
   - Click 'Apply' and 'OK'
3. Program: Path to sass
4. Arguments: ```--no-cache --update $FileName$:$ProjectFileDir$/static/css/$FileNameWithoutExtension$.min.css --style compressed```
5. Output paths to refresh: ```../css/$FileNameWithoutExtension$.min.css:../css/$FileNameWithoutExtension$.min.css.map```
6. OK > Apply > OK


## Project structure

```
raspberry_mini_smart_home
│   .env
│   requirements.txt
│   requirements-dev.txt
│   .gitignore
│   README.md
├───apps
│   ├───accounts
│   ├───core
│   ├───door_opener
│   └───watering_system
├───locale
│   └───pl
├───project
│   │   asgi.py
│   │   celery_config.py
│   │   routing.py
│   │   settings.py
│   │   urls.py
│   └   wsgi.py
├───static
│   ├───css
│   └───js
└───templates
    │ base.html
    ├───core
    ├───door_opener
    └───watering_system
```
