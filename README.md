1. Creando imagenes usando Podman y BuildAh

#Creacion de imagen
cd ~/code/files/images/
cd ~/code/files/images/containerfiles/10_devtools/02_dbclient/oracle-cli/

bat ./1.0-x64-fed39/Dockerfile
vim ./1.0-x64-fed39/Dockerfile
podman build --build-arg UID=1000 --build-arg GID=1000 -t lucianoepc/oracle-cli:1.0-x64-fed39 ./1.0-x64-fed39/


#Prueba ejecutando un contenedor en background:
podman run --name oracle-cli-1 -d lucianoepc/oracle-cli:1.0-x64-fed39
podman container ls --all
podman exec -it oracle-cli-1 /bin/bash
podman container kill oracle-cli-1

#Prueba ejecutando un contenedor en forma interactiva:
podman run --name oracle-cli-1 -it --rm lucianoepc/oracle-cli:1.0-x64-fed39

#Eliminar la capas de imagenes temporales generados durante la contruccion
podman image prune

2. Creando imagenes usando ContainerD y BuildKit


#Inicializando
systemctl --user start containerd.service
systemctl --user start buildkit.service

#Creacion de imagen
cd ~/code/files/images/
cd ~/code/files/images/containerfiles/10_devtools/02_dbclient/mssql-cli/

bat ./1.0-x64-alp3.19/Dockerfile
vim ./1.0-x64-alp3.19/Dockerfile
nerdctl build --build-arg UID=1000 --build-arg GID=1000 -t lucianoepc/mssql-cli:1.0-x64-alp3.19 ./1.0-x64-alp3.19/


#Prueba ejecutando un contenedor en background:
nerdctl run --name mssql-cli-1 -d lucianoepc/mssql-cli:1.0-x64-alp3.19
nerdctl container ls --all
nerdctl exec -it cnt-tools-1.0.0 /bin/bash
nerdctl container kill cnt-tools-1.0.0

#Prueba ejecutando un contenedor en forma interactiva:
nerdctl run --name mssql-cli-1 -it --rm lucianoepc/mssql-cli:1.0-x64-alp3.19

3. Otras opciones

#Limpieza de una imagen para volver a generarla con el mismo tag
nerdctl image ls
nerdctl container ls --all

nerdctl container rm XXXXX


#Subir la imagen 
nerdctl login -u lucianoepc
nerdctl push lucianoepc/basic-tools:1.0.0-alpine

4. Usar la imagen en Kubernates

#Probar en Kubernates (namespace 'ppoc-lpenac')
oc run net-tools1 -it --rm --image=lucianoepc/basic-tools:1.0.0-alpine --restart=Never -- bash

bat podo-nettools-1_alpine.yaml
bat podo-nettools-root_alpine.yaml

oc create -f podo-nettools-1_alpine.yaml
oc get pod -n ppoc-lpenac
oc exec pod/podo-nettools-1 -it -n ppoc-lpenac -- bash

oc delete pod/podo-nettools-1 -n ppoc-lpenac


