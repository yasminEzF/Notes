# Yocto Project Intro

## Intro

### names

1. yocto
2. openEmbedded
   - Board Support Packages **BSP**
   - Software Packages
3. poky
    - dunfell (LTS)
    - kirkstone (LTS)

### Terminologies

#### 1. Metadata

contains configurations `local.conf`

- `meta-Yocto-bsp`
  BSP layer provided by **poky**
- `meta-poky`
  SW pkgs provided by **poky**
- `meta-skeleton`
  a template for guidance when creating a BSP layer

#### 2. Layer

#### 3. Recipe

#### 4. bitbake

a generator provided by poky

![image](https://github.com/yasminEzF/iti_notes/assets/109252157/174941c2-6227-4536-aaf8-e1435087436c)

1. parse configurations
2. fetch
3. patch
4. configure
5. output source
6. package
    - .rpm
    - .debian
    - .ipk
7. QA test
8. package feed
    - dpkg
    - apt
9. output
    - image
    - SDK

## Setting up

in `yocto/poky/`, run this command to:

- append bit-bake to PATH, and be able to use in terminal as a command
- create `build` directory (default) for output distro

```bash
$source oe-init-build-env
```

### important files

in `build/conf/`

- `local.conf`
  choose `MACHINE`
  set a `downloads` directory (save data usage)
  set a `SSTATE_CACHE` directory (save rebuilding past files)

- `bblayer.conf`
  set up layers that contain desired recipes

### bitbake

```bash
$bitbake add-layer
```

adds layer to `bblayer.conf`

```bash
$bitbake recipe.bb
```

fetches, patches, compiles and installs
