# Sentiment Analysis application 
![](https://cdn-images-1.medium.com/max/1600/1*oH9lP-4GWT8eQHh_X5obsw.gif)
## Dockerfile

- frontend (`./frontend/Dockerfile`):
  - use the `nginx:1.15` base image
  - copy the content of the `build` directory in `/usr/share/nginx/html`
  - expose the port 80, remember to map the ports if you want to run it
- webapp (`./webapp/Dockerfile`):
  - use the `openjdk:8-jdk-alpine` base image
  - expose port 8080
  - copy `target/sentiment-analysis-web-0.0.1-SNAPSHOT.jar` at `/` of the container
  - at start the container should execute `java -jar sentiment-analysis-web-0.0.1-SNAPSHOT.jar --sa.logic.api.url=${SA_LOGIC_API_URL}`
    - the SA_LOGIN_API_URL env variable will be the address at which webapp will find the logic service, for the moment let's leave it empty
- logic (`./logic/Dockerfile`):
  - use `python:3.6.6-alpine` as base image
  - copy the content of the `sa` directory in the directory `/app` inside the container
  - put yourself inside the `/app` directory of the container
  - execute `pip3 install -r requirements.txt`
  - execute `python3 -m textblob.download_corpora`
  - expose port 5000
  - at startup the container should execute `python3 sentiment_analysis.py`

- Build all the containers with by running `docker build .`, remember to tag the images ( -t <docker-hub-username>/<image-name>)
- We'll test the interactions between containers with the docker-compose, so go on at the next step

## docker-compose.yaml
- Write a `docker-compose.yaml` to test your application locally, you can use the previously built images or you can directly specify how to build  you images directly in the docker-compose
- mount the `build` directory in `/usr/share/nginx/html` of the frontend container
- set the `SA_LOGIC_API_URL` env variable in the webapp container to `http://<dns_of_logic>:5000` (5000 will be the port of the logic app)
- expose the frontend on port 80 and webapp on port 32000 of localhost
- for the frontend to be able to comunicate with the webapp you have to modify the `/etc/hosts` for linux or `c:\Windows\System32\Drivers\etc\hosts` for windows adding a line `<ip-of-webapp> app-sentiment`

## Kubernetes
- push the 3 images previously built to docker hub, pay attention to the tag
- create the needed resources to deploy your application on kubernetes
- remember that webapp and frontend have to be reachable from outside the cluster, choose port 32000 as NodePort
  - modify appropriately the hosts file
- check that everything is working through your browser

## Credits

Credits to [Rinor Maloku](https://medium.freecodecamp.org/learn-kubernetes-in-under-3-hours-a-detailed-guide-to-orchestrating-containers-114ff420e882) for the code and the original article.
