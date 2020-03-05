## Development Installation

In development Bilara uses polymer serve for the client content and flask to serve the API.

Prequisites:

### Clone the repo

I assume that bilara is cloned to ~/bilara
```
git clone git@github.com:suttacentral/bilara.git
```

### Setup Node environment

polymer-cli should be installed. I recommend installing nvm: https://github.com/creationix/nvm to use a modern version of node which should result in no problems. Once nvm is installed (remember to open a new terminal), run something like:
```
nvm install node
npm install -g polymer-cli
cd ~/bilara/client
npm install
```

### Setup python environment
The API server uses python3.6+, I reccomend installing [pyenv](https://github.com/pyenv/pyenv), you can use this command to install pyenv and some useful plugins: `curl https://pyenv.run | bash` it may tell you to add some lines to your .bashrc

Once pyenv is installed (remember to open a new terminal), run something like:
```
pyenv install 3.7.2
cd ~/bilara/server
pyenv virtualenv 3.7.2 bilara
pyenv local bilara
pip install -r requirements.txt
```

### Setup ArangoDB

Make sure arangoDB is installed and running, if using Suttacentral you can start the docker instance
```
docker start sc-arangodb
```

You need to create a user and database for bilara, this can be done via the web admin

* Go to http://localhost:8529
* Login as root, (password "test" if using suttacentral development environment)
* Choose the _system database
* Go to "users" in the side panel
* Add user, set username, name and password fields to "bilara"
* Go to "databases" in the side panel
* Add database, name "bilara", set the user for the database to bilara



### Clone the data repository

Note that in development mode bilara does not automatically perform git actions so you must manage the repository yourself

```
cd ~/bilara
git clone git@github.com:suttacentral/bilara-data.git repo
```

## Running development

The quick way:
```
cd ~/bilara
./run_dev.sh
```

Otherwise if you wish to get seperate debug feedback from the client and server code, run in seperate terminals:

```
cd ~/bilara/server
flask run
```

```
cd ~/bilara/client
npm serve
```

## Use the local site

at http://localhost:5000
