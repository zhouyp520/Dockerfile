# Guestbook Sample with redis
## create sample guestbook html with php
frontend:
  image: kubeguide/guestbook-php-frontend:latest
  container_name: frontend
  links:
  -  redis-master
  -  redis-slave
  ports:
    - "30001:80"
## data read/write with redis
### write data:frontend->redis-master->redis-slave
redis-master:
  image: redis:6.0.4
  container_name: redis-master
## #read data: frontend---------------->redis-slave
redis-slave:
  image: redis:6.0.4
  command: redis-server --replicaof redis-master 6379 --bind 0.0.0.0
  container_name: redis-slave
  links:
  - redis-master
