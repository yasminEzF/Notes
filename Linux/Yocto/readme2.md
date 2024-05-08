# Yocto Project (Creating Recipes)

## recap

metadata includes

- layers
- BSP
- configuration
- recipe
- bbclass
- bbappend

`meta` by openEmbedded
contains recipes for most popular packages (openSSH, apache, python, dropbear, ...)

## Creating a layer

create a directory and fill in the layer manually

```bash
$mkdir meta_newLayer
```

or use `bitbake-layers` to create layer with template files

```bash
$bitbake-layers create-layer ~/yocto/poky/meta_newLayer
```

generated directory hierarchy

```vim
meta_newLayer/
├── conf
│   └── layer.conf
├── COPYING.MIT
├── README
└── recipes-example
    └── example
        └── example_0.1.bb
```

### `conf/layer.conf`

```vim
# We have a conf and classes directory, add to BBPATH
BBPATH .= ":${LAYERDIR}"

# We have recipes-* directories, add to BBFILES
BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"

BBFILE_COLLECTIONS += "meta_newLayer"
BBFILE_PATTERN_meta_newLayer = "^${LAYERDIR}/"
BBFILE_PRIORITY_meta_newLayer = "6"

LAYERDEPENDS_meta_newLayer = "core"
LAYERSERIES_COMPAT_meta_newLayer = "dunfell"
```

### `COPYING.MIT`

licence file

### `README`

a template for the layer documentation

### `recipes-example/example/example_0.1.bb`

```vim
SUMMARY = "bitbake-layers recipe"
DESCRIPTION = "Recipe created by bitbake-layers"
LICENSE = "MIT"

python do_display_banner() {
    bb.plain("***********************************************");
    bb.plain("*                                             *");
    bb.plain("*  Example recipe created by bitbake-layers   *");
    bb.plain("*                                             *");
    bb.plain("***********************************************");
}

addtask display_banner before do_build
```

## Recipes

a layer can contain many recipes

running `$bitbake recipe.bb` generates packege from recipe

a recipe typically consists of `header part` and `development part`

```vim
#########################################
#               HEADER PART             #
#########################################
DESCREPTION
AUTHOR
LICENCE


#########################################
#           DEVELOPMENT PART            #
#########################################


```
