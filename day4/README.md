# cloud4c-cicdb4

### build and testing understanding 

<img src="build.png">

### pipeline jobs 

<img src="pipe.png">

### pipeline plugins 

<img src="plugins.png">

## app jenkins data

### index.html 

```

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ashu-webapp</title>
</head>
<body>
   <h1> hello world </h1>
    <h2> I m ashutoshh SIngh trying to write code </h2>
    <p> updating docker image version tag </p>
    <b> Showing jenkins image </b>
    <img src="jk.jpg" alt="not loading" width="600" height="400">

</body>
</html>
```

### health.html 

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>health</title>
</head>
<body>
    <h1> Just for checking health of HTTP request</h1>
    
</body>
</html>
```

### .dockerignore

```
Dockerfile
docker-compose.yaml
.dockerignore
```

### Dockerfile

```
FROM nginx
LABEL name="ashutoshh singh"
COPY . /usr/share/nginx/html/
EXPOSE 80
```

### docker-compose.yaml

```
version: '3.8'
services: # app components like ui , logs , db 
  ashu-ui-service:
    image: ashu-webui:version$BUILD_NUMBER 
    build:
      context: . # location of dockerfile 
      dockerfile: Dockerfile  # name of Dockerfile 
    container_name: ashu-web-c1 # name of container 
    ports:
      - 1234:80 # you can use 1024-5000 range 
```


