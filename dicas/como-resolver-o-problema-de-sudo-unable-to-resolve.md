# como resolver o problema de sudo unable to resolve host

digamos que o nome da sua máquina seja maquinaxx

verifique os arquivos /etc/hostname e /etc/hosts

em /etc/hosts , se não tiver , adicione :

```bash
127.0.0.1 localhost
127.0.0.1 maquinaxx
```