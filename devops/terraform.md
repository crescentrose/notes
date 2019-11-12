# Terraform

## Terraform

[terraform.io](https://www.terraform.io)

> Terraform is a tool for building, changing, and versioning infrastructure safely and efficiently. Terraform can manage existing and popular service providers as well as custom in-house solutions.

### Quickstart

* figure out a provider to use \(Amazon, GCP, DigitalOcean...\)
* procure necessary details from the cloud provider

| Provider | Guide | What you need |
| :--- | :--- | :--- |
| AWS | \[1\] | Access key, secret key, region |
| Google | [2](https://www.terraform.io/docs/providers/google/getting_started.html) | Project, region, zone, service account |

* create a .tf file with basic details

```text
provider "google" {
  project = "projectname"
  region = "us-central"
  zone = "us-central1-c"
}
```

* add details about the resources you want to instantiate, e.g. a VPS:

```text
resource "google_compute_instance" "default" {
  name = "terraform"
  machine_type = "f1-micro"

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-9"
    }
  }

  network_interface {
    network       = "default"
    access_config = {
    }
  }
}
```

* plug in your credentials:
  * for AWS, you can just put them in the .tf file
  * for GCP, you need the json key and place its path in the

    `GOOGLE_CLOUD_KEYFILE_JSON` env variable
* run `terraform init` to set up the project
* run `terraform apply` to apply the config
* voila! you are now spending money
* run `terraform destroy` to tear down the VPS.

### Concepts

* at the beginning, Terraform loads all `.tf` files from the current directory
* a _provider_ is a cloud service, e.g. Google Cloud, AWS, Azure...
* a _resource_ is an object on the cloud service, things like virtual machines,

  DNS entries, networks, S3 buckets... you get the point

  * a _resource_ has multiple parameters that can be found in the documentation,

    and those parameters can also depend on other resources \(e.g. we want to

    initialize a VPS from our own image, we can define the image first and then

    build the machine on top of it\)

  * if a dependency is not required everything will run in parallel :\)
  * changing a resource's parameters will reinstance the provided resource
    * yes, it will tear down an entire VPS if you want to change its memory
    * that's one way to force you to have your entire infrastructure codified...

* resources can have _provisioners_ - scripts that run after a resource is

  created 

#### Provisioners

* add a `provisioner` block to an existing `resource`

```text
resource "google_compute_instance" "default" {
  name = "terraform"
  machine_type = "f1-micro"

  // ...

  provisioner "local-exec" {
    command = "echo ${google_compute_instance.default.network_interface.0.access_config.0.nat_ip} > ip.txt"
  }
}
```

* multiple provisioners are supported
* they are only run when a resource is **created**:
  * do not use them to manage configuration on an existing server
  * use Terraform to invoke a real configuration management solution
  * or don't, I'm not telling you what to do 🤷
* after this provisioner runs, the public IP of the instance will be in

  `ip.txt`

* if provisioning fails the resource is _tainted_ - it will be removed and

  recreated on next execution plan

* the resources are **not rolled back** when the plan fails
* provisioners can also run on destroy action.

#### Variables, secret keys, etc.

Keeping access keys in `.tf` files is bad news bears, so you should put it into parameters. This is less of an issue with the GCP provider because the keyfile is pulled from your local system through an ENV variable, but other things can be parameterized as well.

Into `variables.tf` it goes:

```text
variable "machine_type" {
  default = "f1-micro"
}

variable "some_secret_key" {}
```

Now you can access the variables from the main Terraform files:

```text
resource "google_compute_instance" "default" {
  name = "terraform"
  machine_type = "${var.machine_type}"
}
```

**Setting variables**

In descending order of precedence:

1. Command-line flags, e.g. `terraform apply -var "foo=bar"`
2. From a file:
   1. matching `terraform.tfvars`
   2. matching `*.auto.tfvars`
   3. files loaded with the -var-file flag
3. From environment variables: `TF_VAR_name`
4. From UI input
5. From defaults, if they exist.

**Data types**

| Type | Example |
| :--- | :--- |
| string | \`"foo"\`\` |
| list | \`\["foo", "bar"\]\`\` |
| map | `{ "foo" = "bar" }` |

#### Output variables

Output variables are a way to expose data about infrastructure that you care about in an organised way. Straight up:

```text
output "ip" {
  value = "${google_compute_instance.default.network_interface.0.access_config.0.nat_ip}"
}
```

This then shows up on the output of `terraform apply` and you can also query for it afterwards with `terraform output ip`.

#### Modules

Modules are pre-built infrastructure building blocks for services. e.g. if you want to deploy [Vault](vault) to GCP there is a module for that, just drag and drop it into the .tf file and you are ready to start spending money!

Other than pre-built modules, this could be useful for things like separating web and database servers in a big project, separating frontend and backend servers in an even bigger project, but that is probably not what you are going to be working on immediately after completing a basic Terraform tutorial.

#### Workspaces

Workspaces are basically different environments - you can have one for development, staging, QA, production etc. Workspaces are isolated from other workspaces' state.

Use `terraform workspace new <name>` to create a new workspace.

#### Terraform Remote

Terraform supports **remote backends** which are basically hosts for Terraform state files so that everyone in your team is working with the same state. There are plenty of options for a remote backend, from [Consul](consul) over plain old S3 to the majestic monolith that is Terraform Enterprise.

```text
terraform {
  backend "consul" {
    address = "demo.consul.io"
    path = "some-unique-identifier"
    lock = false
  }
}
```

To move between different backend servers or local storage, just run `terraform init`.

### Automating all the things!

It's all fun and games until you actually have to integrate Terraform with your workflow. To integrate with a CI/CD pipeline, Terraform docs suggest using a remote backend to sync the state, to run Terraform with `input=false` command line option to indicate that Terraform should not attempt to ask for input and to have the plan reviewed by a human

You can skip the approval process by adding `-auto-approve` to the `apply` command.

You can also use `terraform workspace` to select a specific environment, e.g. development, staging, production etc.

#### Integrate with Packer

This is probably the main point of Terraform: you provision a machine with Terraform and then deploy a [Packer](packer) image on the machine. Since Packer can build Docker images, you can potentially provision a machine with Docker preinstalled and just run a Docker image on it. However right now I am lacking the knowledge to put the pieces together nicely.

### Recipes

#### Remotely execute commands on GCP

* set up a Compute Firewall rule to allow SSH connections

```text
resource "google_compute_firewall" "external_ssh" {
  name = "external-ssh"
  network = "default"

  allow {
    protocol = "tcp"
    ports = ["22"]
  }

  source_ranges = ["0.0.0.0/0"]
  target_tags = ["externalssh"]
}
```

* we use the `externalssh` tag to limit external SSH access only to the machines

  that we create this way

* additionally, we need a key to push and to add metadata on the provisioned

  machine:

```text
resource "google_compute_instance" "example" {
  name         = "example"
  machine_type = "f1-micro"
  zone         = "us-central1-a"
  tags         = ["externalssh"] // important!

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-9"
    }
  }

  network_interface {
    network = "default"

    access_config {
    }
  }

  provisioner "remote-exec" {
    connection {
      type        = "ssh"
      user        = "${var.user}"
      timeout     = "45s"
      private_key = "${file("~/.ssh/id_rsa")}"
    }

    inline = [
      "echo Hello world!",
    ]
  }

  # Ensure firewall rule is provisioned before server, so that SSH doesn't fail.
  depends_on = ["google_compute_firewall.external_ssh"]

  metadata {
    ssh-keys = "ivan:${file("~/.ssh/id_rsa.pub")}"
  }
}
```

#### Run in tandem with Ansible

This builds on top of the previous recipe.

* install Python on remote host through `remote-exec`
* run Terraform through `local-exec`

```text
provisioner "remote-exec" {
  inline = [
    "apt-get -qq install python -y",
  ]
}

provisioner "local-exec" {
  working_dir = "./ansible"
  command = <<BASH
    ansible-playbook -u root --private-key ~/.ssh/id_rsa \
      -i ${google_compute_instance.default.network_interface.0.access_config.0.nat_ip}, \
      playbook.yml
BASH

  environment {
    PUBLIC_IP = "${google_compute_instance.default.network_interface.0.access_config.0.nat_ip}"
  }
}
```

Now you can use any Ansible playbook to set up the provisioned machine.

