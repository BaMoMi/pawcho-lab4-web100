# PAwChO - Laboratorium 4 - web100

Projekt zawiera Dockerfile budujący obraz `web100` z serwerem Apache na bazie `ubuntu:latest`.

**Student:** Wiktor Fura  
**E-mail:** s101560@pollub.edu.pl  
**Grupa dziekańska:** IO6

## Budowanie obrazu

```bash
docker image build --file Dockerfile --tag web100:latest .
```

## Uruchomienie kontenera

```bash
docker run --name web100_test -d -p 8080:80 web100:latest
curl http://localhost:8080
```

Po teście można zatrzymać i usunąć kontener:

```bash
docker stop web100_test
docker rm web100_test
```

## Sprawdzenie warstw obrazu

```bash
docker inspect web100:latest --format '{{len .RootFS.Layers}}'
docker image history web100:latest
```

## Wysłanie obrazu do DockerHub

Zamień `bamomi` na swój login DockerHub, jeżeli jest inny.

```bash
docker login
docker tag web100:latest bamomi/web100:latest
docker push bamomi/web100:latest
```

Przykładowy link do repozytorium DockerHub po wysłaniu obrazu:

```text
https://hub.docker.com/r/bamomi/web100
```
