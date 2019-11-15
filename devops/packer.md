# Packer

Packer is a tool to generate various kinds of deployable software images on all kinds of platforms, such as VirtualBox, AWS or GCP. The most useful \(to me\) portion of the software is generating Docker images using a more consistent format, integration with existing provisioning tools such as Ansible and smaller resulting images due to not keeping intermediary layers in the final image \(although this can be alleviated with multi-stage builds with vanilla Docker\).

Likewise, using Packer means not using Docker's image building component - Packer treats Docker as just another container runtime environment instead of a full suite of container utilities. This lets you hedge your bets slightly in case another hip and cool container engine comes along in a few years \(MegaDocker or something like that\).

## Use YAML instead of JSON

As of version 1.4.2 \(Aug 2019\), Packer supports only JSON as its configuration format. HSL2 format support is reportedly in the works, but until it lands you might want to use a simple Ruby oneliner to write your configuration in YAML and then port it to JSON:

```bash
ruby -r json -r yaml -e 'puts(YAML.load($stdin).to_json)'
```

You can use a rudimentary {M,R}akefile, a Bash script or another task runner of your choice to simplify this.

{% code title="yacker.sh" %}
```bash
#!/bin/sh

# Usage: ./yacker.sh [yaml_configuration.yml] [packer_params]
# e.g.: ./yacker.sh packer.yml validate

YAML_FILE=$1
shift
PACKER_PARAMS=$*

CONVERTER="ruby -r json -r yaml -e 'puts(YAML.load(\$stdin).to_json)'"

eval "cat $YAML_FILE | $CONVERTER | packer $PACKER_PARAMS -"
```
{% endcode %}

This lets you write pretty YAML and convert it to ugly JSON on the fly. This also means that from here on \(at least until HSL2 support is released\) the examples in this article will be in YAML, not JSON, as it is much easier to write.

_This **code** is released under_ [_CC0_](https://creativecommons.org/publicdomain/zero/1.0/)_. Please use it in any way that you see fit, or don't at all!_

## Docker images with Packer

Packer makes writing Dockerfiles much easier - it \(almost\) completely removes them from the equation.

Let's say you want to [look at pretty trains](https://github.com/mtoyoda/sl) in your terminal using Docker. A simple Packer configuration to accomplis such an image might look like this:

```yaml
builders:
- type: docker
  image: ubuntu
  commit: true
  changes:
    # You MUST specify an entrypoint and a command, otherwise this will
    # trip you up.
    - 'ENTRYPOINT ["/bin/bash"]'
    - 'CMD ["-c", "/usr/games/sl"]'
provisioners:
- type: shell
  inline:
  - apt-get update
  - apt-get install -y sl
post-processors:
- type: docker-tag
  repository: sample-image
  tag: latest
```

Note the following parts:

* explicitly defining your ENTRYPOINT and CMD seems tedious, but ensures you won't inherit an unwanted entrypoint from the base image
* you can use Packer's provisioning system to run arbitrary commands on the image you're building. This is `shell` in this example, but it could be anything - even \(spoiler alert\) Ansible!
* you specifically instruct which tag to use in the configuration file, _but_ you can also pull this from somewhere else \(ENV variable, someplace else\) using Packer variables

Building this configuration will leave you with a tagged image that you can then push to your favorite Docker registry. For bonus points, this could also be used to build a, say, Vagrant image with the same provisioners, so you don't have to re-do this if you want to target a different platform.

## Provisioning with Ansible

_TODO: write about this_



