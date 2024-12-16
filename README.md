##### nvidia_cuda_undefined_symbol_bug_workaround


# For error: undefined symbol... libnvJitLink.so.12
```error
env/lib/python3.12/site-packages/torch/lib/../../nvidia/cusparse/lib/libcusparse.so.12: undefined symbol: __nvJitLinkComplete_12_4, version libnvJitLink.so.12
```

## Overall
- Problem: dynamic linker (ld-linux.so) cannot find libnvJitLink.so.12 when running your CUDA application
- Solution: path to libnvJitLink.so.12 as LD_LIBRARY_PATH must be found and set manually: in every python venv env, starting every env session (when alternating between many envs, maybe)

# Solution in Two Steps
Recommended: Do both steps in the python venv env before starting your ipynb (if you are using a notebook). After entering your venv env:
```bash
source env/bin/activate
```
perform the steps.

The first step will work in an ipynb notebook BUT the 2nd step will NOT work when run in a notebook. So set the path before starting 'jupyter notebook'.

## Steps:
1. find the path:
- Run in termial
```bash
find / -name libnvJitLink.so.12 2>/dev/null  # Suppress error messages
```
the output may include various versions that you don't want.
e.g. cached files, envs, containers, installations, etc.
```
/home/abc/.cache/bazel/_bazel_abc/b0476c413c941aacfe3480843ee50b1f/external/cuda_nvjitlink/lib/libnvJitLink.so.12
```
vs.
```
/usr/lib/x86_64-linux-gnu/libnvJitLink.so.12
```
or
```
/home/abc/code/bert/env/lib/python3.12/site-packages/nvidia/nvjitlink/lib/libnvJitLink.so.12
```

2. set the path TO the file:
- NOT the full path with the file itself, just the directory WHERE the file is 
- not in quotes
- with forward slashes / before and after /
```bash
export LD_LIBRARY_PATH=<path_to_lib_dir>:$LD_LIBRARY_PATH
```
or
```bash
export LD_LIBRARY_PATH=/YOUR*DIR*PATH*HERE/:$LD_LIBRARY_PATH
```
e.g.
```bash
export LD_LIBRARY_PATH=/usr/lib/x86_64-linux-gnu/:$LD_LIBRARY_PATH
```
e.g. or per venv env
```bash
export LD_LIBRARY_PATH=/home/oops/code/bert/env/lib/python3.12/site-packages/nvidia/nvjitlink/lib/:$LD_LIBRARY_PATH
```




# References:

- https://forums.developer.nvidia.com/t/error-while-loading-shared-libraries-libnvjitlink-so-12/264308/10

- https://discuss.pytorch.org/t/undefined-symbol-nvjitlinkadddata-12-1-version-libnvjitlink-so-12/190166

- https://forums.developer.nvidia.com/t/libnvjitlink-so-12-not-being-linked-automatically/292095

- https://github.com/siliconflow/onediff/issues/1061


## Discussion / Description
The core issue is that the dynamic linker (ld-linux.so) cannot find libnvJitLink.so.12 when running your CUDA application. This library is part of the CUDA toolkit and is necessary for just-in-time compilation of CUDA kernels. The solution involves ensuring that the directory containing this library is in the LD_LIBRARY_PATH environment variable.
Here's a breakdown of the troubleshooting and solution process:

#### 1. Locate All libnvJitLink.so.12 files
The first step is to find the actual location of the library file on your system. Use the find command:
- Run in Terminal:
```bash
find / -name libnvJitLink.so.12 2>/dev/null  # Suppress error messages
```

#### 2. Find The One
If you have multiple CUDA installations, containers, caches, and venv envs, you will likely find multiple instances of this file. Make a note of the correct path associated with the CUDA version you're using. The path should resemble something like this (but will vary depending on your installation):


- Example paths:
```
/usr/local/cuda-12.2/targets/x86_64-linux/lib/libnvJitLink.so.12 
```
or 
```
/data/nvhpc/Linux_x86_64/23.1/cuda/12.2/targets/x86_64-linux/lib/libnvJitLink.so.12 
```

#### 3. Set Path 
Once you know the directory containing libnvJitLink.so.12, add it to the LD_LIBRARY_PATH environment variable. This is temporary but you likely need it to be temporary, as every env and application will expect a new path:
- Run in terminal:
```bash
export LD_LIBRARY_PATH=<path_to_lib_dir>:$LD_LIBRARY_PATH
```

## Versions
As dependency chain versions are a forever-puzzle, 
example pip freeze -> requirements.txt may be in repo.


