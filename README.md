# Metaphor Interpretation Web Service

The code in this repository provides both the end-user interface and the
JSON API for handling requests to interpret metaphors.

## Installation

The directory structure should look like this:

![Directory tree](docs/directories.png)

The instructions below assume you're installing in /research, but this can
be any directory you prefer.

This is how to install the entire metaphor pipeline, including the web
service in this repository:

1. Clone repositories for the Metaphor pipeline and for the Henry
   abductive reasoner:

```
mkdir -p /research/repo
cd /research/repo
git clone https://github.com/isi-metaphor/Metaphor-ADP.git metaphor
git clone https://github.com/isi-metaphor/henry-n700.git henry
```

2. Compile Henry:
- Install dependencies: python-dev, libsqlite3-dev, graphviz and python-lxml
- Compile:

```
cd /research/repo/henry
make -B
```

3. Install Boxer:
- Install Prolog: `sudo apt-get install swi-prolog`
- The official Boxer Subversion repository is no longer available, so
  clone our unofficial Git repository:

```
cd /research/repo
git clone https://github.com/jgordon/boxer
cd boxer
make
make bin/boxer
make bin/tokkie
```
- Uncompress the model in the Boxer installation directory:
```
cd /research/repo/boxer
tar xvjf models-1.02.tar.bz2
```

4. Install Gurobi: Install gurobi in a separate subdirectory of
   installation-dir (called gurobi maybe), and install the license file
   somewhere.

5. Clone the web service and set up its directories:

```
cd /research/rep
git clone https://github.com/isi-metaphor/lcc-service.git

mkdir -p /research/temp/lcc-service.tmp
mkdir -p /research/data/metaphor_kbs
mkdir -p /research/data/lcc-service
mkdir -p /research/logs/lcc-service
```

6. Install dependencies:

```
sudo apt-get install fabric openssh-server screen
sudo pip install jinja2 django lz4 gitpython pexpect regex sexpdata simplejson
```

7. Deploy the web service: The git clone command just downloads the code
   but then it needs to be configured and deployed.

- Edit the file `lcc-service/fab/config.{branch_name}.json` depending on if
  you want to install the prod or dev branch of the web service
- For local deployment -- that is, on the same machine:
  - Check the function install in fabfile.py to make sure it runs:
    `basicConfig(branch_name)`, `localConfig()`, `deploy()`
  - Execute `fab install:branch_name`
- For remote deployment -- that is, the deployment directory is on another
  machine to which you have ssh access without password:
  - Edit the ssh username and ssh private key to use (`remoteConfig` function
    in fabfile.py)
  - Make sure you replace `localConfig()` with `remoteConfig()` in the install
    function in fabfile.py
  - Execute `fab install:branch_name`

8. Initialize the database file:
- Go into the deployed directory and run:
```
python manage.py syncdb --noinput --settings=lccsrv.settings
```
- Create a user for the web interface by running:
```
python manage.py createsuperuser --username=username_to_create \
    --email=whatever@whatever --settings=lccsrv.settings
```

9. Run:
- Edit the shell.sh script to set the name of the screen session it'll start
  (the -S option)
- Run the `shell.sh` command
- Within the screen it started, start `tempRun.sh`; for monitored mode
  run `run.sh`.
- Disconnect from the screen (CTRL-A D)

## Acknowledgments

This work was supported by the Intelligence Advanced Research Projects
Activity (IARPA) via Department of Defense US Army Research Lab contract
W911NF-12-C-0025. The US Government is authorized to reproduce and
distribute reprints for Governmental purposes notwithstanding any
copyright annotation thereon. Disclaimer: The views and conclusions
contained herein are those of the authors and should not be interpreted
as necessarily representing the official policies or endorsements,
either expressed or implied, of IARPA, DoD/ARL, or the US Government.
