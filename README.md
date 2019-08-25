# willoucom.github.io

## Install ruby env
sudo apt-get install ruby-full build-essential zlib1g-dev

## Create user's gem directory
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

## Install gems
gem install jekyll bundler

## Serve
bundle exec jekyll serve
