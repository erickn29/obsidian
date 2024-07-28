Чтобы добавить новый [[python]] интерпретатор на Ubuntu, следуйте этим шагам:

Обновите списки пакетов:

```
sudo apt update
```

Установите зависимости:

```
sudo apt install -y build-essential libssl-dev zlib1g-dev libncurses5-dev \ libncursesw5-dev libreadline-dev libsqlite3-dev libgdbm-dev libdb5.3-dev \ libbz2-dev libexpat1-dev liblzma-dev tk-dev libffi-dev
```

Скачайте исходный код Python:

Перейдите на сайт python.orghttps://www.python.org/downloads/source/ и выберите нужную версию. Затем выполните команду, чтобы скачать и распаковать исходный код (замените 3.x.x на нужную версию):

```
wget https://www.python.org/ftp/python/3.x.x/Python-3.x.x.tgz
tar -xf Python-3.x.x.tgz
cd Python-3.x.x
```
  `

Соберите и установите Python:

```
./configure --enable-optimizations
make -j $(nproc)
sudo make altinstall
```
  

Обратите внимание на использование altinstall вместо install, чтобы избежать перезаписи системного Python.

Проверьте установку:

```
python3.x --version
```

Замените python3.x на установленную версию, например, python3.10.

Теперь у вас должен быть установлен новый интерпретатор Python на вашей системе.