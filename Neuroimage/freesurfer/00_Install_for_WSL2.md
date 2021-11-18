* version: Ubantu-20.04

* The workflow were based on [FS7_wsl - Free Surfer Wiki (harvard.edu)](https://surfer.nmr.mgh.harvard.edu/fswiki//FS7_wsl)

# Install X-server in Windows

* [FSL_wsl_xming - Free Surfer Wiki (harvard.edu)](https://surfer.nmr.mgh.harvard.edu/fswiki/FSL_wsl_xming)

  

#  Install Freesurfer on WSL2

## Preparation

* `wget https://surfer.nmr.mgh.harvard.edu/pub/dist/freesurfer/dev/freesurfer_7-dev_amd64.deb`
* regist from [FreeSurfer Registration form (harvard.edu)](https://surfer.nmr.mgh.harvard.edu/registration.html) and get the `license.txt` file from your E-mail



## Install

* `sudo apt-get update`
* `sudo apt-get install ./freesurfer_7-dev_amd64.deb`
  * Error of `libffi6`, `freesurfer : Depends: libffi6 (>= 3.0.4) but it is not installable`
  * `wget http://mirrors.edge.kernel.org/ubuntu/pool/main/libf/libffi/libffi6_3.2.1-8_amd64.deb`
  * `sudo apt install ./libffi6_3.2.1-8_amd64.deb`



## Configure

add the following to your `$HOME/.bachrc`

> export XDG_RUNTIME_DIR=$HOME/.xdg
>
> export DISPLAY={your_IP_address}:0
>
> export FREESURFER_HOME=/usr/local/freesurfer/7-dev
>
> export FS_LICENSE={path/to/your}/license.txt
>
> source /usr/local/freesurfer/7-dev/SetUpFreeSurfer.sh

the IP address can get from `grep nameserver /etc/resolv.conf`

add the IP address to `X0.hosts` file from windows side in `Xming` 

