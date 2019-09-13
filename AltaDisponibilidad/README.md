# Script para crear un entorno con alta disponibilidad

Aquí tienes todos los comandos que utilizamos en nuestras clases para lograr tener nuestro entorno listo para alta disponibilidad.

Comienza por crear el grupo de recursos en donde vas a tener todas tus herramientas. El grupo de recursos será lo único con un nombre en español, recuerda es una buena idea crear recursos en inglés por cuestiones de acentos y caracteres que no podrían ser bien identificados.
```b
az group create --name grupoRecursosPlatzi --location westus
```
Después del grupo de recursos, crea la dirección IP pública que vamos a utilizar.
```b
az network public-ip create --resource-group grupoRecursosPlatzi --name myPublicIP
```
Con tu dirección pública creada podrás entonces crear un balanceador de cargas (Load Balancer) y asociar esta dirección al balanceador.
```b
az network lb create --resource-group myResourceGroupLoadBalancer --name myLoadBalancer --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --public-ip-address myPublicIP
```
Con el balanceador creado, podremos crear una sonda, te permite saber el estado de "salud" de tu balanceador de cargas.
```b
az network lb probe create --resource-group myResourceGroupLoadBalancer --lb-name myLoadBalancer --name myHealthProbe --protocol tcp --port 80
```
Crea ahora también una regla de red que permita la comunicación por un puerto específico (para acceder a sitios web el puerto es 80).
```b
az network lb rule create --resource-group myResourceGroupLoadBalancer --lb-name myLoadBalancer --name myLoadBalancerRule --protocol tcp --frontend-port 80 --backend-port 80 --frontend-ip-name myFrontEndPool --backend-pool-name myBackEndPool --probe-name myHealthProbe
```
Al terminar con tu balanceador de cargas, puedes continuar con la creación de tu red virtual.
```b
az network vnet create --resource-group myResourceGroupLoadBalancer --name myVnet --subnet-name mySubnet
```
Además de la red virtual, también necesitas un grupo de seguridad de red.
```b
az network nsg create --resource-group myResourceGroupLoadBalancer --name myNetworkSecurityGroup
```
En el recién creado grupo de seguridad debes también crear una regla de acceso.
```b
az network nsg rule create --resource-group myResourceGroupLoadBalancer --nsg-name myNetworkSecurityGroup --name myNetworkSecurityGroupRule --priority 1001 --protocol tcp --destination-port-range 80
```
!Esta parte me divierte mucho! Una mezcla de bash con las herramientas de Azure, hacemos un ciclo para crear tres interfaces de red y asignarles valores, esta es la manera definitiva de repetir el ciclo tantas veces como quieras dependiendo de las Máquinas virtuales que vayas a querer usar, por cada Máquina virtual debes tener una interfaz de red, estas interfaces son el medio por el que las máquinas se van a conectar.
```b
for i in `seq 1 3`; do
    az network nic create \
        --resource-group myResourceGroupLoadBalancer \
        --name myNic$i \
        --vnet-name myVnet \
        --subnet mySubnet \
        --network-security-group myNetworkSecurityGroup \
        --lb-name myLoadBalancer \
        --lb-address-pools myBackEndPool
done
```
Ve ahora por el conjunto de disponibilidad.
```b
az vm availability-set create --resource-group myResourceGroupLoadBalancer --name myAvailabilitySet
```
Por último, en cuanto a configuración, la parte que más hemos visto, las Máquinas virtuales. Recuerda tener en tu misma ruta el archivo [cloud-init.txt](https://github.com/aminespinoza/ContenidoIaaS/tree/master/AltaDisponibilidad/cloud-init.txt) para poder enviarlo a la MV, esto tiene una gran secuencia de errores así que recuerda tener mucho cuidado con esto.
```b
for i in `seq 1 3`; do
    az vm create \
        --resource-group myResourceGroupLoadBalancer \
        --name myVM$i \
        --availability-set myAvailabilitySet \
        --nics myNic$i \
        --image UbuntuLTS \
        --admin-username azureuser \
        --generate-ssh-keys \
        --custom-data cloud-init.txt \
        --no-wait
done
```
Con toda esta configuración solo usa este comando para poder obtener tu dirección IP.
```b
az network public-ip show --resource-group myResourceGroupLoadBalancer --name myPublicIP --query [ipAddress] --output tsv
```
Recuerda tomar un par de minutos para esperar que toda la configuración esté hecha.

