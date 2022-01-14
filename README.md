This repo is an attempt to use Pants for a monorepo consisting of two "application" projects - app1
and app2 - and two "library" projects - lib1 and lib2. Additionally, I want to use this project in
PyCharm, which means PyCharm should understand which modules and dependencies are available for 
each project, and I should be able to run tests on each project as well. 

I'm using Python 3.9.9 and running this on MacOS 11.6.2.

The setup I have here is - app1 depends on lib1, while app2 depends on both lib1 and lib2. Each 
project has a requirements.txt file; the dependencies themselves do not matter, they're just there 
to ensure that each application project can gain the dependencies of each library project that it 
depends on.

To set this up using Pants, I've done the following per the [Pants getting started guide]
(https://www.pantsbuild.org/docs/getting-started):

1. Installed Pants 2.9.0
2. Added `enabled=false` to `[anonymous-telemtry]` in `pants.toml` to make the telemetry warning 
   go away
3. Added a .gitignore file 
4. Added source roots to `pants.toml` of - app1, app2, lib1, lib2
5. Enabled the Python backend

Logging from running `./pants tailor`:

```
08:48:59.25 [INFO] Initializing scheduler...
08:48:59.48 [INFO] Scheduler initialized.
Created app1/BUILD:
  - Add python_sources target app1
  - Add macro python_requirements
Created app2/BUILD:
  - Add python_sources target app2
  - Add macro python_requirements
Created lib1/BUILD:
  - Add python_sources target lib1
  - Add macro python_requirements
Created lib2/BUILD:
  - Add python_sources target lib2
  - Add macro python_requirements
```

At this point, something seems off because if I run `./pants dependencies`, I only see the lib1
dependency:

```
app1/__init__.py
app1/app1_main.py
app1:requirements.txt
app2/__init__.py
app2/app2_main.py
app2:requirements.txt
lib1/__init__.py
lib1/my_lib1.py
lib1:pandas
lib1:requirements.txt
lib2/__init__.py
lib2/my_lib2.py
lib2:requirements.txt
```

**QUESTION** Why is the lib1 dependency the only one to show up from a requirements.txt file?

6. I next create a venv named "env" and configure PyCharm to use that as the interpreter. 
7. To make PyCharm happy, I run "pip install" on each of the 4 requirements files, though this 
   of course results in the single heavyweight venv that the Pants docs say I shouldn't need. 
   But I'm at least now able to code in PyCharm.
8. I then add test_my_lib1.py to lib1 and run `./pants tailor`. I'm then able to run `./pants 
   test ::` successfully. However, I can't run the test in PyCharm yet since pytest is not yet 
   in my venv. 

**QUESTION** What is the recommended approach for running a test in PyCharm? In my non-Pants 
project, we have a separate requirements_test.txt file for declaring test dependencies. I could do
the same thing here and then "pip install" those into the venv and also add that file to my BUILD
file. 

**QUESTION** I'm also wondering how I control which version of Pytest is used by Pants. I am 
thinking that if I use the approach above - declaring test dependencies (including Pytest) via a 
separate requirements_test.txt file that I then register in my BUILD file - that Pants would then 
use that version of Pytest. 

**QUESTION** In Java/Gradle projects, I use dependencies to declare that app1 depends on lib1 
and app2 depends on lib1 and lib2. I don't see how to do that yet; it seems like any project can 
depend on any other project. Is that correct? 