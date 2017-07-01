# como  mover um diretorio para um novo volume no openstack
tags: openstack,dreamcompute, linux

O passo a passo que se segue compreendo todas as atividades de criação do volume até a copia e configuração do fstab.

## Passos

1. Criar um novo volume e vincular(attach) a instância, fazer isso no painel de controle do openstack
2. Criar a partição no linux

   1. Para verificar se o volume foi atachado corretamente:

```bash
sudo fdisk -l
-- ou --
ls /dev/vd*
````

   2. para criar a partição

```bash
sudo fdisk /dev/vdc
```
O fdisk apenas vai gravar os dados no final. 
* Usar o comando **"m"** para ver a lista de opções.
* Crie uma nova partição no disco com **"n"**, 
* depois use a opção **"p"** para criar uma partição primária, 
* depois selecione **1** para informar que é a primeira partição, 
* depois pressione **enter** 2 vezes para indicar o início e o fim da partição, 
* depois disso entre **"t"** para selecionar o tipo de partição , 
* então entre 83 (tipo linux), 
* pressione **"p"** para ver as opções selecionadas e 
* logo depois pressione **"w"** para gravar

Deve-se efetuar um **"Soft Reboot Instances"** antes de continuar, esse reboot não acarreta a perda do ip e é rápido.
Para fazer isso deve-se antes efetuar o _logout do ssh_ e depois acessar as instâncias do projeto e selecionar a opção **"Soft Reboot Instances"** .

Logout in the SSH and reboot the instance by going to Projects > Instances, select the checkbox next to your instance and click the "Soft Reboot Instances

Deve criar a partição **/dev/vdc1** .

Para formatar com *ext4* usar:

```bash
sudo mkfs.ext4 /dev/vdb1
```

Para montar os dados deve-se antes pegar o UUID e criar uma pasta para efetuar o mount, faça isso com o seguinte comando:

```bash
sudo blkid /dev/vdb1
/dev/vdb1: UUID="51751b87-a583-42b3-8d61-27ed586ba8da" TYPE="ext4" PARTUUID="0a505f5e-01"

-- crie uma pasta para fazer a montagem --

sudo mkdir -p /mnt/data

```

Eu prefiro montar inicialmente para a pasta /mnt/data mesmo que depois eu vá montar em definitivo em outro local.

Altere o /etc/fstab:

```bash
LABEL=cloudimg-rootfs   /        ext4   defaults        0 0
UUID=51751b87-a583-42b3-8d61-27ed586ba8da       /mnt/data   ext4    defaults        0   2
```

Teste a configuração com:

```bash
sudo mount -a
```

Caso precise desmontar usar:

```bash
umount -l /dev/vdc
```

3. Copiar os arquivos e configurar o fstab

Com o */mnt/data* montado você pode copiar os arquivos do */home* para a nova pasta

```bash
rsync -avH /home/ /mnt/data
```

Existe a advertência de não usar o **cp -avr** ou o **cp -ar** , o rsync deve ser melhor.

Depois que copiou alterar o **/etc/fstab** para:

```bash
LABEL=cloudimg-rootfs   /        ext4   defaults        0 0
UUID=51751b87-a583-42b3-8d61-27ed586ba8da       /home   ext4    defaults        0   2
```

Agora uma dica importante para apagar os antigos arquivos em /home:

```bash
$ sudo mount --bind / /mnt
-- cuidado nesse ponto: --
$ sudo rm -rf /mnt/home
$ sudo umount /mnt
```
ou:
```bash
$ sudo mount --bind / /mnt
$ cd /mnt
$ sudo rm -rf home
$ cd /
$ sudo umount /mnt
```



### links:

[fonte 1 - darwinbiler](http://www.darwinbiler.com/openstack-creating-and-attaching-a-volume-into-an-instance/)

[fonte 2 - dreamhost](https://help.dreamhost.com/hc/en-us/articles/221778268-How-to-create-and-mount-volumes-on-DreamCompute)

[fonte 3 - askubuntu](https://askubuntu.com/questions/656/how-to-move-usr-to-a-new-partition)
