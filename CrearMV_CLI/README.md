# Scripts para crear una máquina virtual desde CLI

Aquí tienes el script que escribiste en la clase en donde hablamos de cómo crear una máquina virtual con línea de comandos.

Primero crea por el grupo de recursos.
```b
az group create --name myResourceGroup --location eastus
```
Después el comando que más interesa, el de crear una MV.
```b
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --authentication-type  \
  --admin-password "miPasswordFavorito"
```

Como tip, te cuento que puedes escribir los parámetros de dos maneras posibles, ambas son perfectamente válidas, solo usa la que mejor te acomode.
```b
--resource-group --name

-g -n
```
Ahora la pregunta que nos queda es
