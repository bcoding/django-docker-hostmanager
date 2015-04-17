# django-docker-hostmanager
This django app is part of a homegrown docker host management system.

## Contents

* `docker_hostmanager`
contains a simple django project containing the REST API and a Django admin
* `docker_hostmanager.api`:
This contains a django app which knows how to manage containers on the docker host(s).
* `docker_hostmanager.rest`:
This contains a django app which makes `docker_hostmanager.api` accessible as REST API 

## API

POST /container_group - Neue Container-Gruppe definieren
```json
{
    "name": "openslides-1.7",
    "image": "OpenSlides/openslides-1.7",
    "static_containers": {
        "static_files": {
            "volumes": ["/static"],
            "command": "pyhton manage.py collectstatic --noinput",
            "environment": {
                "STATIC_ROOT": "/static",
                ...
            }
        }
    },
    "web_resources": [
        {
            "domain": "static.openslides.de",
            "path": "1.7",
            "volume": "static_files->/static"
        }
    ]
}
```

POST /containers - Neuen Container starten
```json
{
    "group": "openslides-1.7",
    "vhost": {
        "protocol": "https",
        "domains": ["openslides.acme.net", "acme.openslides.de"],
        "path": "/event1",
	...
    },
    "web_resources": [
        {
            "protocol": "%(vhost.protocol)",
            "domains": ["openslides.acme.net", "acme.openslides.de"],
            "path": "/media",
            "volume": "fce96b8d030a27046da05207d0a52f5109958c1affc2965a338c62b775259f49->/media"
        }
    ],
    "container": {
        "volumes_from": ["fce96b8d030a27046da05207d0a52f5109958c1affc2965a338c62b775259f49"],
        "environment": {
            "DATABASE_URL": "sqlite:///data/openslides.sqlite",
            "STATIC_URL": "%(vhost.protocol)sstatic.openslides.de/1.7",
            "MEDIA_ROOT": "/media"
            ...
        }
    }
```
