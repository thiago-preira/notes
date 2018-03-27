# Notes
Markdown notes with general purpose
## Alias
Alias to make easy develop
Create a file named **.bash_aliases** at your home directory
```
alias svim='sudo vim'
alias timestamp="date +'%Y%m%d%H%M%S'"
alias add-alias="vim /home/tpereira/.bash_aliases"
alias reload-bash="source /home/tpereira/.bashrc"
alias hs="history |grep $1"
alias update='sudo apt-get update && sudo apt-get upgrade'

alias mci="mvn clean install"
alias sonar="mvn clean install sonar:sonar"

alias boot-run="mvn spring-boot:run"
alias maven-deploy="mvn -B -Prelease -Darguments='-DskipTests=false -Dmaven.javadoc.skip=true' release:clean release:prepare release:perform"

alias gac="git add . && git commit -a -m "

```
