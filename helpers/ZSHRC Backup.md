Last update: 2025.06.25
```bash
export WPSCAN_API=$(cat /home/ch3ng/Desktop/hack/resources/wpscan.txt)
export RESOURCE_DIR="/home/ch3ng/Desktop/hack/resources"
export GITHUB_TOKEN="REDACTED"
export PYENV_ROOT="/home/ch3ng/.pyenv"

alias hist="cat ~/.zsh_history | grep "
alias pyenvcreate="python -m venv /home/ch3ng/Desktop/hack/.venv"
alias pyvenvstart="source /home/ch3ng/Desktop/hack/.venv/bin/activate"
alias pyvenvdestroy="rm -rf /home/ch3ng/Desktop/hack/.venv"
alias ghrelease="gh release download -p '*' -D ./release --clobber"

timefake() {
	local timestamp=$(ntpdate -q $1 | cut -d ' ' -f 1,2)
	shift
	faketime $timestamp $*
}

pyenvinit() {
	eval "$(pyenv init --path)"
}
```