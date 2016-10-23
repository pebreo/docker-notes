# Dotfiles


## .zshrc

```bash

dc() {
 docker-compose "$@"
 #docker-compose $1 $2 $3 $4 $5 $6 $7 $8 $9 $10 $11 $12 $13 $14 $15 $16 $17 $18 $19 $20
}

dma() {
 #docker-machine "$@"
 docker-machine $1 $2 $3 $4 $5 $6 $7 $8 $9 $10 $11 $12 $13 $14 $15 $16 $17 $18 $19 $20
}

dit() {
 docker exec -it $1 /bin/bash
}
alias dbash=dit

deval() {
 eval "$(docker-machine env $1)"
}

dcompleterebuild() {
 dc stop
 dc rm -f
 dc build
 dc up -d 
}

alias dallstop='docker stop $(docker ps -a -q)'
alias dallrm='docker rm $(docker ps -a -q)'
```


## startdev.sh
```
#!/bin/zsh

docker-machine start dev1
eval "$(docker-machine env dev1)"

```