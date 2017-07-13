# Como converter do mercurial para o git


## passo a passo rápido

```bash

cd ~/Desktop
git clone git://repo.or.cz/fast-export.git
git init git_repo
cd git_repo
~/Desktop/fast-export/hg-fast-export.sh -r /path/to/old/mercurial_repo
git checkout HEAD

```
## notas

Dependendo do repositorio do hg talvez não seja possível devido a existência , por exemplo, de multiplas *head* . Para resolver esse tipo de problema o melhor é fazer um clone do repositorio original do HG e depois efetuar as mudanças necessárias na estrutura do repositorio. Essas mudandças podem envolver ouso do strip ou de merge.

Para usar o strip altere o __.hg/hgrc__ adicione:

```bash
[extensions]
mq =
```

Os principais comandos para usar no hg são:

* hg log -G -- exibe o log de forma gráfica
* hg log -l 3 -- exibe as última 3 mudanças
* hg commit --close-branch -m "close branch"
* hg merge [REV]


[link 1](http://hivelogic.com/articles/converting-from-mercurial-to-git/)

