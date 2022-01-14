This repo is an attempt to use Pants for a monorepo consisting of two "application" projects - app1
and app2 - and two "library" projects - lib1 and lib2. app1 depends on lib1, while app2 depends
on both lib1 and lib2. Each project has a requirements.txt file; the dependencies themselves do not
matter, they're just there to ensure that each application project can gain the dependencies of each
library project that it depends on.

To set this up using Pants, I've done the following per the [Pants getting started guide]
(https://www.pantsbuild.org/docs/getting-started):

1. Installed Pants 2.9.0
2. Added `enabled=false` to `[anonymous-telemtry]` in `pants.toml` to make the telemetry warning 
   go away