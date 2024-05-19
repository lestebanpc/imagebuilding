#Ejemplo del comando de creación/compilación de la imagen:
#  > podman build --build-arg UID=1000 --build-arg GID=1000 -t quyllur/postgresql-cli-alp3.19:1.0-x64 ./workdir
#

#-------------------------------------------------------------------------------------
# Layer final
#-------------------------------------------------------------------------------------
FROM alpine:3.19.1

#1. Parametros de entrada
ARG UID
ARG GID

#2. Constantes
ARG USER_NAME=appuser
ARG HOME_PATH=/home/appuser

#3. Crear el usuario/Grupo para desarrollo e instalar git/sudo
RUN apk --no-cache add bash git sudo \
    busybox-extras openssh-client  \
    #bind-tools net-tools netcat-openbsd iputils tcpdump nmap nmap-scripts nmap-ncat \
    postgresql-client \
    && addgroup -g $GID ${USER_NAME} \
    && adduser -D -u $UID -G ${USER_NAME} -h ${HOME_PATH} -s /bin/bash ${USER_NAME} \
    && mkdir -p ${HOME_PATH} \
    && chown $UID:$GID ${HOME_PATH}
    #&& echo "${USER_NAME} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers \


#4. Descargar archivos de usuario
USER ${UID}:${GID}
RUN git clone https://github.com/lestebanpc/dotfiles.git ${HOME_PATH}/.files

#5. Instalar paquetes basicos (openssl, fzf, vim) y comandos basicos
#   > Parametros de '00_setup_summary.bash':
#     01> Tipo de invocación (sin menu): 1/2 (sin usar un menu interactivo/no-interactivo)
#     02> Opciones de menu a ejecutar: entero positivo.
#     03> ID de los repositorios de comandos a configurar, separados por coma. Si no desea configurarse ninguno envie "EMPTY".
#     04> ID de los paquetes del repositorio del SO, separados por coma, a instalar si elige la opcion de menu 1024. Si desea usar el los los paquetes por defecto envie "EMPTY".
#         Los paquetes por defecto que son: Curl, UnZip, OpenSSL y Tmux.
#     05> Ruta donde se descargaran los programas (de repositorios como github). Si se envia vacio o EMPTY se usara el directorio predeterminado "/opt/tools" o "~/tools".
#     06> Ruta base donde se almacena los comandos ("CMD_PATH_BASE/bin"), archivos man1 ("CMD_PATH_BASE/man/man1") y fonts ("CMD_PATH_BASE/share/fonts").
#         Si se envia vacio o EMPTY se usara el directorio predeterminado.
#            > Comandos      : "/usr/local/bin"      (para todos los usuarios) y "~/.local/bin"         (solo para el usuario actual)
#            > Archivos man1 : "/usr/local/man/man1" (para todos los usuarios) y "~/.local/man/man1"    (solo para el usuario actual)
#            > Archivo fuente: "/usr/share/fonts"    (para todos los usuarios) y "~/.local/share/fonts" (solo para el usuario actual)
#     07> Ruta de archivos temporales. Si se envia vacio o EMPTY se usara el directorio predeterminado.
#     08> El estado de la credencial almacenada para el sudo.
#     09> Flag '0' para limpiar el cache de paquetes del sistema operativo. Caso contrario, use 1.
#     10> Actualizar los paquetes del SO. Por defecto es 1 (false), si desea actualizar use 0.
#     11> El GID y UID del usuario que ejecuta el script, siempre que no se el owner de repositorio, en formato "UID:GID"
#  > Opción de menu a usar: Paquetes basicos 'openssl,fzf' (1024) + VIM (32768) 
USER 0:0
RUN bash ${HOME_PATH}/.files/setup/linux/00_setup_summary.bash 2 33792 'jq,yq,fd,bat,ripgrep,delta,oh-my-posh' 'openssl,fzf' 'EMPTY' 'EMPTY' 'EMPTY' -1 0 1 "$UID:$GID"

#6. Configurar el profile del usuario como Editor (incluyendo a VIM como Editor)
#   > Parametros de '00_setup_summary.bash':
#     01> Tipo de invocación (sin menu): 1/2 (sin usar un menu interactivo/no-interactivo)
#     02> Opciones de menu a ejecutar: entero positivo.
#     03> ID de los repositorios de comandos a configurar, separados por coma. Si no desea configurarse ninguno envie "EMPTY".
#     04> ID de los paquetes del repositorio del SO, separados por coma, a instalar si elige la opcion de menu 1024. Si desea usar el los los paquetes por defecto envie "EMPTY".
#         Los paquetes por defecto que son: Curl, UnZip, OpenSSL y Tmux.
#     05> Ruta donde se descargaran los programas (de repositorios como github). Si se envia vacio o EMPTY se usara el directorio predeterminado "/opt/tools" o "~/tools".
#     06> Ruta base donde se almacena los comandos ("CMD_PATH_BASE/bin"), archivos man1 ("CMD_PATH_BASE/man/man1") y fonts ("CMD_PATH_BASE/share/fonts").
#         Si se envia vacio o EMPTY se usara el directorio predeterminado.
#            > Comandos      : "/usr/local/bin"      (para todos los usuarios) y "~/.local/bin"         (solo para el usuario actual)
#            > Archivos man1 : "/usr/local/man/man1" (para todos los usuarios) y "~/.local/man/man1"    (solo para el usuario actual)
#            > Archivo fuente: "/usr/share/fonts"    (para todos los usuarios) y "~/.local/share/fonts" (solo para el usuario actual)
#     07> Ruta de archivos temporales. Si se envia vacio o EMPTY se usara el directorio predeterminado.
#     08> El estado de la credencial almacenada para el sudo.
#     09> Flag '0' para limpiar el cache de paquetes del sistema operativo. Caso contrario, use 1.
#     10> Actualizar los paquetes del SO. Por defecto es 1 (false), si desea actualizar use 0.
#     11> El GID y UID del usuario que ejecuta el script, siempre que no se el owner de repositorio, en formato "UID:GID"
#  > Opción de menu a usar: Configurar el profile del usuario (1) + VIM como Editor 'Crear archivos, Descargar plugins e indexarlos' (4)
USER ${UID}:${GID}
RUN bash ${HOME_PATH}/.files/setup/linux/00_setup_summary.bash 2 5 'EMPTY' 'EMPTY' 'EMPTY' 'EMPTY' 'EMPTY' -1 1 1 "$UID:$GID"

#6. Establecer el shell predeterminado 'ash' a 'bash'
#CMD [ "/bin/bash" ]
ENTRYPOINT [ "/bin/bash" ]

