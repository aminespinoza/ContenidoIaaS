# Comandos para instalar software adicional en tu Máquina virtual

Para poder instalar software en Ubuntu utilizas apt, el gestor de aplicaciones para distribuciones de Linux basadas en Debian.

En el caso del video, viste como instalar Apache. Para hacer esto, usas los siguientes comandos.
```b
sudo apt-get update
```

Puedes también ahorrar unos cuantos caracteres si haces lo siguiente, será lo mismo.
```b
sudo apt update
```

Instala el paquete de apache
```b
sudo apt install apache2
```

Verifica que el servicio está listo
```b
sudo systemctl status apache2
```

¡Estás listo! A disfrutar de tu servidor web en Ubuntu.
