# Scripts de automatización

¿Quieres un truco aquí? Si ya viste el video de las extensiones aquí tienes un truco muy bueno. Con este comando podrás ver una lista completa de todas las extensiones disponibles en cierta ubicación.
```b
az vm extension image list --location westus --output table
```
Recuerda que aquí podrás ejecutar archivos tipo .sh si es que tu máquina utiliza Linux. También puedes utilizar JSON como el siguiente archivo, este es el archivo que viste en el video.
```b
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```


