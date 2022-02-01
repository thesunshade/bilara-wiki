Starting from a virgin install of Ubuntu 20.04 Minimal

# Install git and other apt-gettable dependencies

```
sudo apt install curl git nodejs npm python-is-python3 python3-distutils python3-venv

```

# Clone the repository

```
git clone git@github.com:suttacentral/bilara.git
```

Note: Setting up your SSH key with github is outside the scope of these instructions.

## Clone the data repo
```
cd bilara
git clone git@github.com:suttacentral/bilara-data.git repo

```

# Install Python Dependencies

Bilara uses *python 3.8.x*

## Install Poetry

Poetry is a dependency manager for Python. Install it using the following script:

```
curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python
source $HOME/.poetry/env
```

Make sure it is installed correctly by running:

```
poetry -V
```

## Install Python Dependencies

```
cd ~/bilara
poetry install
```

If the installed python version is incorrect, open a new terminal and check the python version on you machine:

```
python -V
```

Make sure `bilara/.python-version` has the same version.

# Install Node Dependencies

```
cd ~/bilara/client
npm install
```

# Install (or configure) ArangoDB

## If you installed the SuttaCentral docker ecosystem 

You can simply use the docker ArangoDB installed for it

You can create a file `local.sh` which will run local commands before running the bilara server. Run the following to create a local.sh which will make sure the ArangoDB container is running.

```
cd ~/bilara
echo '#/bin/bash
docker start sc-arangodb
sleep 2' > local.sh
chmod +x local.sh
```

Then you need to create a `bilara` user, run the following:

```
docker exec -it sc-arangodb arangosh
```

It will ask for a password, which is `test` by default.

In the ArangoSH shell enter the following:
```
db._createDatabase("bilara", {}, [ {username: 'bilara', passwd: 'bilara', active: true}]);
```

That will create the required database and user. Hit `ctrl-d` to exit the shell.

## If you *haven't* installed the SuttaCentral ecosystem

You should install ArangoDB 3.6.x in some manner, then create the database and user in some manner.

# Create local settings

You can configure the local development setup to do things such as masquerading as a user. Create a file named `server/local_config.py` with contents such as:
```
config = {
    'LOCAL_USERNAME': 'sujato',
    'LOCAL_LOGIN': 'sujato',
    'LOCAL_EMAIL': 'bob@example.com',
    'GIT_SYNC_ENABLED': False,
}
```

# Start Bilara

```
cd ~/bilara
./run-dev
```

And navigate to http://localhost:3003/

## Index Translation Memory / Search

You should not need to do this manually, but the TM can be forcefully (re)indexed with this command:

```
./index-tm
```

