Github Action code to do this without using someone's github action:
https://www.reddit.com/r/devops/comments/1028hvo/i_was_tired_of_renewing_my_resume_each_year_so_i/

```
name: CI

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@master
    - name: Create needed directories
      run: |
        mkdir output
        sudo mkdir /usr/share/fonts/truetype/firasans/
        sudo mkdir /usr/share/fonts/truetype/firacode/
    - name: Install LaTeX and download required fonts
      run: |
        sudo apt install texlive -y
        sudo apt install texlive-xetex -y
        sudo apt install texlive-latex-extra -y
        sudo apt install texlive-fonts-extra -y
        #sudo apt install ghostscript -y
        sudo apt install texlive-binaries -y
        #echo ttf-mscorefonts-installer msttcorefonts/accepted-mscorefonts-eula select true | sudo debconf-set-selections
        #sudo apt-get install ttf-mscorefonts-installer
        curl https://fonts.google.com/download?family=Fira%20Sans -o firasans.zip
        curl https://fonts.google.com/download?family=Fira%20Code -o firacode.zip
        sudo unzip -o firasans.zip -d /usr/share/fonts/truetype/firasans/
        sudo unzip -o firacode.zip -d /usr/share/fonts/truetype/firacode/
        fc-cache -fv
        sudo fc-cache -fv
    - name: Run xetex to compile LaTeX files
      run : |
        xelatex resume.tex -output-directory=./output
    - name: Find where PDF is output to
      run: |
        find . -iname "*.pdf"
    - name: Upload resume PDF as artifact
      uses: actions/upload-artifact@master
      with:
        name: Resume
        path: ./resume.pdf
```
