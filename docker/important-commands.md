### Pulling images from DockerHub

```bash
docker pull nginx
docker images
docker run nginx
```

### Running different versions of an Image

```bash
docker pull python
docker pull python:3.7
docker images python

docker run --rm python:latest python --version
```

This command does a few things:

1. **docker run** creates and starts a new container
2. **--rm** tells Docker to remove the container after it exits
3. **python:latest** specifies which image to use
4. **python --version** is the command to run inside the container

### Listing and Removing Images

```bash
docker images
docker rmi python:3.7
```

However, you might instead see an error message like this:
```bash
Error response from daemon: conflict: unable to remove repository reference "python:3.7" (must force) - container <container_id> is using its referenced image <image_id>
```

```bash
docker ps -a
docker rm <container-id>
docker images python
```

### Understanding Image Layers

Docker images are built using a layered filesystem. Each layer represents a set of filesystem changes. This layered approach allows Docker to be efficient with storage and network usage.

First, let's inspect the layers of the Nginx image we pulled earlier:

```bash
docker inspect --format='{{.RootFS.Layers}}' nginx
```

You'll see output similar to this:

```bash
[sha256:2edcec3590a4ec7f40cf0743c15d78fb39d8326bc029073b41ef9727da6c851f sha256:e379e8aedd4d72bb4c529a4ca07a4e4d230b5a1d3f7a61bc80179e8f02421ad8 sha256:b5357ce95c68acd9c9672ec76e3b2a2ff3f8f62a2bcc1866b8811572f4d409af]
```

Each of these long strings (called S**HA256 hashes**) represents a layer in the image. Each layer corresponds to a command in the Dockerfile used to build the image.

To better understand layers, let's create a simple custom image. First, create a new file named Dockerfile in your current directory:

```bash
nano Dockerfile
```

In this file, add the following content:

```bash
FROM nginx
RUN echo "Hello from custom layer" > /usr/share/nginx/html/hello.html
```

This Dockerfile does two things:

1. It starts with the Nginx image we pulled earlier (**FROM nginx**)
2. It adds a new file to the image (**RUN echo...**)

Now let's build this image:
```bash
docker build -t custom-nginx .
docker images
docker inspect --format='{{.RootFS.Layers}}' custom-nginx
```

Understanding layers is crucial because:

- Layers are cached, speeding up builds of similar images
- Layers are shared between images, saving disk space
- When pushing or pulling images, only changed layers need to be transferred

### Searching for Images on Docker Hub

```bash
docker search nginx
```

### Saving and Loading Images

Docker allows you to save images as tar files and load them later. This is useful for transferring images between systems without using a registry, or for backing up images.

```bash
docker save nginx > nginx.tar
ls -lh nginx.tar
```

Now, let's load the image back from the tar file:
```bash
docker load < nginx.tar
```

This process of saving and loading images can be very useful when you need to:
- Transfer images to a system without internet access
- Back up specific versions of images
- Share custom images with others without using a registry

### Image Tagging Basics

Tagging is a way to create aliases for your Docker images. It's commonly used for versioning and organizing images. Let's explore how to tag images.

```bash
docker tag nginx:latest my-nginx:v1
```

This command creates a new tag **my-nginx:v1** that points to the same image as **nginx:latest**. Here's what each part means:

- **nginx:latest** is the source image and tag
- **my-nginx** is the new image name we're creating
- **v1** is the new tag we're assigning

```bash
docker run -d --name my-nginx-container my-nginx:v1
```

This command does the following:

- **-d** runs the container in **detached** mode (in the background)
- **--name my-nginx-container** gives a name to our new container
- **my-nginx:v1** is the image and tag we're using to create the container

Tagging is useful for several reasons:

1. **Version control:** You can tag images with version numbers (v1, v2, etc.)
2. **Environment separation:** You might tag images for different environments (dev, staging, prod)
3. **Readability:** Custom tags can make it clearer what an image is for

Remember, tags are just aliases - they don't create new images, they just create new names that point to existing images.
