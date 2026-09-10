```zsh
# 1 
wget https://gh-proxy.com/https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-x86_64.sh -O /tmp/Miniforge3.sh
# 2
bash /tmp/Miniforge3.sh -b -p /root/miniforge3
/root/miniforge3/bin/conda init zsh
source ~/.zshrc
# 3 
mamba create -n ampere-env python=3.12 -y
```
