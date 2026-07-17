# Update packages
sudo apt update && sudo apt upgrade -y
sudo apt autoremove --purge -y
sudo apt clean

# Git
git fetch --prune

# Docker
docker system df
docker builder prune -a

# npm
npm cache verify

# Check disk usage
du -h --max-depth=1 /root | sort -hr

# Check WSL disk usage
df -h
