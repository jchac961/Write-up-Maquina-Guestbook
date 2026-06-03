<img width="671" height="278" alt="Captura de pantalla 2026-06-02 230549" src="https://github.com/user-attachments/assets/ea4d62ad-62da-4b02-83ba-d6715703b05d" />

# Write-up-Maquina-Guestbook

Maquina de la plataforma www.whoami-labs.com

Iniciamos la maquina vulnerable

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 123358" src="https://github.com/user-attachments/assets/353a0b79-f4be-44d4-b041-e872b581912d" />

# RECONOCIMIENTO

Una vez iniciada la maquina vulnerable realizamos un escaneo de puertos utilizando la herramienta nmap

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 123413" src="https://github.com/user-attachments/assets/55fb57bb-6a58-4dbe-984c-8baeb0bce307" />

Dentro de los puertos encontrados tenemos

✳️Puerto #22 corriendo un ssh en la version OpenSSH 9.2p1 Debian 2+deb12u10 (protocol 2.0)

✳️Puerto #80 corriendo un http en una version Apache httpd 2.4.67 ((Debian))

Ya teniendo los puertos fuimos a un navegador a verificar que contenia el mismo, en primera instancia nos encontramos con una pagina que contenia un cuadro de dialogo que permitia ingresar un nombre de usuario y un mensaje

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 123447" src="https://github.com/user-attachments/assets/dc865962-6a1c-4c11-83b2-801026bf17b9" />

Verifique su codigo en busqueda de alguna pista

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 123840" src="https://github.com/user-attachments/assets/e3b6e73c-b379-4bfe-af99-8b48b0ac2c05" />

Luego realice un fuzzing utilizando la herramienta feroxbuster, En el cual encontre otra ruta mas, asi que procedi a verificarla 

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 130136" src="https://github.com/user-attachments/assets/0b09bdba-d050-4a92-aa68-5e774f79bb59" />

Esta ruta estaba bloqueada al ingreso normal y solo estaba permitida a usuarios administradores

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 130149" src="https://github.com/user-attachments/assets/aa096803-8801-4b71-9b6d-44105265b551" />

Hice algunas pruebas enviando usuarios y mensajes y nuevamente me los reflejaba en la pagina

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 130506" src="https://github.com/user-attachments/assets/4787d34b-1106-47f3-9391-c09d37199f84" />

Luego trate de hacer una inyeccion de payload xss a ver si estaba sanitizada la pagina y en el frontend en efecto estaba sanitizadda y nos envio un mensaje en el cual nos decia que se bloquearia por seguridad

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 130706" src="https://github.com/user-attachments/assets/7816ebab-ca1c-4e3b-a370-312e90cb7d8a" />

Luego volvimos a enviar nuevamente un mensaje pero en esta ocasion interceptamos la peticion con la herramienta burp suite

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 130812" src="https://github.com/user-attachments/assets/85d8fe75-c138-4a8c-a1d4-f86768d7ed38" />

Reenviamos la peticion al repeater para luego intentar hacer el robo de cookie de sesion

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 130908" src="https://github.com/user-attachments/assets/d229c1c6-e832-43b9-99a6-ac2ece3f6e08" />

Luego cambiamos el mensaje por el payload xss que nos permitiria obatener la cookie, tambien pusimos nuestra maquina en modo escucha utilizando el servidor http el en puertto #5000

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 132936" src="https://github.com/user-attachments/assets/69e818a9-f211-48b5-b651-8d73d995f15c" />

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 133345" src="https://github.com/user-attachments/assets/e4d3b05c-8eb2-4d4b-98a3-eb443e8ce3a9" />

Obtuvimos la coockie de sesion

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 133358" src="https://github.com/user-attachments/assets/1ffb7728-13b8-47e3-b697-65e85b2504cd" />

En mi caso no habia ninguna sesión anterior asi que lo que hice fue agregarla para tratar de obtener el acceso al login de la pagina

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135137" src="https://github.com/user-attachments/assets/ac339f03-82b5-46e9-983a-457091ccabe4" />

Procedi a refrescar la pagina y tuve acceso al sistema, dentro contenia las credenciales de un usuario 

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135148" src="https://github.com/user-attachments/assets/48dae5be-0f59-4854-b82c-e6b6d79de79e" />

# EXPLOTACION

Ya teniendo las credenciales procedi a tratar de ingresar a la maqquina por medio de ssh y obtuve el acceso

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135251" src="https://github.com/user-attachments/assets/8d97c603-ea9d-494d-9de2-13e8e05cd99a" />

Encontramos la flag de usuario 

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135308" src="https://github.com/user-attachments/assets/3e9b4775-dd91-4b11-8ecc-017c5c6e60a0" />

# ESCALADA DE PRIVILEGIOS

Buscamos crontab y capabilities en a ver si contenia para lograr escalar privilegios

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135429" src="https://github.com/user-attachments/assets/84ae2c1b-b49c-44b1-91b2-2c1a9b3beda0" />

Buscamos binarios a ver si encontrabamos alguno mal configurado, pero tambien utilizamos el comando sudo -l el cual nos permite ver los archivos que puedo ejecutar como root y obtuve uno que me permie obtener el acceso como root

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135458" src="https://github.com/user-attachments/assets/ad18c77f-ce33-46c3-ae34-a1bcf26336f8" />

Lo ejecute y obtuve acceso como root

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135544" src="https://github.com/user-attachments/assets/ff51d576-b4bf-43d8-a810-36f3bac2dfd8" />

Busque la flag de root

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135651" src="https://github.com/user-attachments/assets/c0f284e0-b7bc-41fb-907f-5bb9064d748b" />

<img width="1920" height="1140" alt="Captura de pantalla 2026-06-02 135636" src="https://github.com/user-attachments/assets/f55fc6c7-d74f-496e-bfde-29cf7905d57b" />

<img width="2688" height="1596" alt="Gemini_Generated_Image_6m953u6m953u6m95" src="https://github.com/user-attachments/assets/b94223e0-4f9a-4ad6-a9aa-620be7f557c5" />
