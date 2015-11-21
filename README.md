## On-Premises ReadTheDocs (RTD) Docker Container

On-premises setup of ReadTheDocs (RTD) with full support of LaTeX-to-PDF and all the other bells-and-whistles installed at readthedocs.org.

Installed packages include:
* LaTeX
* Doxygen
* Dvipng
* Graphviz

This setup allows private GitHub documentation projects including ones hosted at Visual Studio Online.

GitHub repository urls are of the form:
https://<your-security-token>:x-oauth-basic@github.com/<your_name>/<your_project>.git

VSO Git repository URLs are of the form:
https://<your-security-token>:x-oauth-basic@<your_name>.visualstudio.com/DefaultCollection/_git/<your_project>

This image also removes the infamous account email verification on user sign-up.

### How to Build the Docker Image
Issue the following command to rebuild the Docker image.
```
docker build -t <your_docker_user_name>/readthedocs:latest --rm=true .
```

Note that you will need access to the Internet while building the machine.
Depending on package site availability the building may take a while.

### How to Run the Container
```
docker run -d -it -p 8000:8000 -e "RTD_PRODUCTION_DOMAIN=my_domain.com:8000" --name readthedocs vassilvk/readthedocs
```

The above example starts the container with the assumption that it will be accessed at http://my_domain.com:8000.
Change the port mappings and environment variable `RTD_PRODUCTION_DOMAIN` to reflect your setup.

You need to run the container only once. After that you only need stop and start it:
```
docker stop readthedocs
```
```
docker start readthedocs
```

When you run the container for the first time, it creates a container volume where it stores the RTD data.
Removing the container will delete the container's volume with all the RTD data. Don't remove it unless you want to re-import your RTD projects from scratch.

### Persist the Volume
Once the container is run with the above run command, you can copy the volume to a well-known folder at your host.
This will let you to bind the container's volume to this host folder which will allow you to persist the data even after container deletes.

To find the location where Docker stores the volume, run
```
docker inspect readthedocs
```

1. Look for the `Mounts` property and take a note of the `Source`.
2. Stop the container: `docker stop readthedocs`.
3. Copy that folder to a folder on your host, for example `~\readthedocs.org`

Once you do that, you can delete the container: `docker rm readthedocs`.
Now you can start the container by mounting the host-provided folder like this:
```
docker run -d -it -p 8000:8000 -e "RTD_PRODUCTION_DOMAIN=my_domain.com:8000" -v ~/readthedocs.org:/www/readthedocs.org --name readthedocs vassilvk/readthedocs
```

### How to Access the Application
To access the web application hosted by the container, navigate to http://my_domain.com:8000/ where "my_domain.com" is the address of the container host.
You can access the site with user `admin`, password `admin`.