# docker-reactjs

Install Docker and Docker Compose on Ubuntu using script
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
sudo apt  install docker-compose
sudo chmod 666 /var/run/docker.sock

Create new image:
docker build -t react-image .

Run image:
docker run -d -p 3000:3000 --name react-app react-image

SSH into a container
docker exec -it react-app bash
ls 

Force end process:
docker ps
docker rm react-app -f

Delete docker image permanently:
docker image ls
docker rmi -f --no-prune [repository]

Run React App after changing code (does not need end process) 
docker run -v $(pwd)/src:/app/src -d -p 3000:3000 --name react-app react-image

Run image with not allow creating/changing the code
docker run -v $(pwd)/src:/app/src:ro -d -p 3000:3000 --name react-app react-image

=============

Environment Variable
[1]
In Docker add: ENV REACT_APP_NAME=myname (after "COPY . . ")
In App.js add: <p>{`Hello ${process.env.REACT_APP_NAME}`}</p>

docker run -e REACT_APP_NAME=myname -v $(pwd)/src:/app/src -d -p 3000:3000 --name react-app react-image

[2]
In src add: 
.env (file)
CHOKIDAR_USEPOLLING=true
REACT_APP_NAME=myname

docker run --env-file ./.env -v $(pwd)/src:/app/src -d -p 3000:3000 --name react-app react-image

=============

Docker Compose
Create a file name called docker-compose.yml
1. Run compose docker
docker-compose up -d
2. End compose docker
docker-compose down
3. To build and run compose docker
docker-compose up -d --build

Build for production with NGINX:
Create Dockerfile.dev andDockerfile.prod separately

npm install
npm run build
docker build -f Dockerfile.prod -t docker-image-prod .
docker run --env-file ./.env -v $(pwd)/src:/app/src -d -p 8080:80 --name react-app-prod docker-image-prod
docker stop [docker_image]

Build a dev (add port 3000):
[start]
docker-compose -f docker-compose.yml -f docker-compose-dev.yml up -d --build
[end]
docker-compose -f docker-compose.yml -f docker-compose-dev.yml down

Build a prod (add port 8080):
[start]
docker-compose -f docker-compose.yml -f docker-compose-prod.yml up -d --build
[end]
docker-compose -f docker-compose.yml -f docker-compose-prod.yml down 

Target flag
docker build --target build -f Dockerfile.prod -t multi-stage-example .
