#Ejemplo del comando de creación/compilación de la imagen:
#  > podman build --build-arg UID=1000 --build-arg GID=1000 -t lucianoepc/vim-coc:1.0-x64-alp3.19-net8 ./workdir
#
FROM alpine:3.19.1

#Parametros de entrada
ARG UID
ARG GID

#Constantes
ARG USER_NAME=appuser
ARG HOME_PATH=/home/appuser

#Crear el usuario/Grupo para desarrollo e instalar git/sudo
#    > 'libstdc++' se usa para NodeJS en Alpine/musl
RUN apk --no-cache add bash git libstdc++ sudo openssh-client \
    && addgroup -g $GID ${USER_NAME} \
    && adduser -D -u $UID -G ${USER_NAME} -h ${HOME_PATH} -s /bin/bash ${USER_NAME} \
    && mkdir -p ${HOME_PATH} \
    && chown $UID:$GID ${HOME_PATH} \
    && mkdir -m 755 /opt/tools/ \
    && chown $UID:$GID /opt/tools/ \ 
    && echo "${USER_NAME} ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers


#4. Descargar archivos de usuario
USER ${UID}:${GID}
RUN git clone https://github.com/lestebanpc/dotfiles.git ${HOME_PATH}/.files

#5. Instalar paquetes basicos, comandos basicos, NodeJS/Python y VIM:
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
#   > Opción de menu a usar: Paquetes basicos (1024) + Instalar NodeJS (4096) + Instalar Python (16384) + Instalar paquetes globales de NodeJS (8192) + Instalar VIM (32768)
USER 0:0
RUN bash "${HOME_PATH}/.files/setup/linux/00_setup_summary.bash" 2 62464 'jq,yq,fd,bat,delta,ripgrep,oh-my-posh,kubectl,oc,net-sdk,roslyn,netcoredbg' 'curl,unzip,openssl,tmux,fzf,dotnetlib' \
         'EMPTY' 'EMPTY' 'EMPTY' -1 0 1 "${UID}:${GID}"

#6. Configurar VIM como IDE y configurar el profile como Developer:
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
#   > Opción de menu a usar: User profile (1) + VIM como IDE (8) + Instalar paquetes de usuario de Python (2)
USER ${UID}:${GID}
RUN bash "${HOME_PATH}/.files/setup/linux/00_setup_summary.bash" 2 11 'EMPTY' 'EMPTY' 'EMPTY' 'EMPTY' 'EMPTY' -1 1 1 ""



#Cambiar el shell predeterminado 'ash' a 'bash'
ENTRYPOINT [ "/bin/bash" ]



