# Pipeline

At the moment, the pipeline is unfortunately really frail. The smallest change might break the steps or the whole pipeline itself.

## Problems
- The pipeline need to be updated
  - Some steps are not useful anymore
  - Some steps are not as efficient as they need to be
- The version used for the different libraries are not static and not documented.
- The docker creation takes space at every build because the images are not deleted.
- The Unitary tests are not updated and some might not be called
- The docker cache is not used as it must be
- A Visual Karsys own docker image need to be created and stored on the docker services

## Solution
Create a VM to reinstall properly Jenkins and all the needed service. Document every steps and details.

Recreate the master and dev pipeline for Visual Karsys with updated steps and good security.

ATTENTION : See as many tutorials as needed both for Jenkins and Docker. The pipeline need to be "perfect"
