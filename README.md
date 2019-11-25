# [kitchen-ec2-example](https://newcontext-oss.github.io/kitchen-terraform/tutorials/amazon_provider_ec2.html)

This project is to show the kitchen-ci Terraform Amazon Provider base on the [tutorial](https://newcontext-oss.github.io/kitchen-terraform/tutorials/amazon_provider_ec2.html)

## Clone this project

- In a directory of your choice, clone the github repository :
  
    ```bash
    git clone git@github.com:orlando-pereira/kitchen-ec2-example.git
    ```

- Change into the directory :
  
    ```bash
    cd kitchen-ec2-example
    ```

## How to setup KitchenCI and RBENV (MacOS Mojave 10.14.5) :

- Run `brew install ruby`
- After previous command finish, run `gem install rbenv`, this would give you ability to choose particular version of Ruby. This is a prerequisite.
- Run the following two commands, to setup Ruby environment for the local directory.

    ```bash
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bash_profile
    echo 'eval "$(rbenv init -)"' >> ~/.bash_profile
    ```

- Reload your BASH interpreter or apply the changes to the profile :
  
    ```shell
    source ~/.bash_profile 
    ```

- Verify rbenv is installed properly with :
  
    ```shell
    type rbenv   # → "rbenv is a function"
    ```

- To install the particular version that we need, run the following command in the project directory:
  
    ```shell
    rbenv install 2.3.1
    ```

- Set local version to be used with command :
  
    ```shell
    rbenv local 2.3.1
    ```

- Previous step is going to create a file named .ruby-version, with the following content `2.3.1`

- Next, [Bundler](https://bundler.io) needs to be installed, run `gem install bundler`, this would provide the dependencies that KitchenCI needs. It is going to install the Gems defined in the `Gemfile`


## Setup KitchenCI:

- update your ssh keys on .kitchen.yaml and your testing.tfvars with your key path

```yaml
transport:
  name: ssh
  ssh_key: "~/.ssh/<your_key.pem>"
```

```terraform
key_name      = "your_key.pem"
```

- Install the needed Gems for KitchenCI using Bundle with command :
  
    ```shell
    bundle install
    ```

- Then converge:
  
    ```bash
    bundle exec kitchen converge
    ```

    - it should show similar result to:
  
        ```bash
          ➜  tf_aws_cluster git:(master) bundle exec kitchen converge 
        -----> Starting Kitchen (v1.25.0)
        -----> Converging <default-ubuntu>...
               Terraform v0.12.16
               + provider.aws v2.39.0
        $$$$$$ Running command `terraform workspace select kitchen-terraform-default-ubuntu` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/     tf_aws_cluster
        $$$$$$ Running command `terraform get -update` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/tf_aws_cluster
        $$$$$$ Running command `terraform validate   -var-file="/Users/orlando/GITHUB/terraform/kitchen-tutorial/tf_aws_cluster/testing.tfvars"` in         directory /Users/orlando/GITHUB/terr
        aform/kitchen-tutorial/tf_aws_cluster

               Warning: The -var and -var-file flags are not used in validate. Setting them has no effect.

               These flags will be removed in a future version of Terraform.

               Success! The configuration is valid, but there were some validation warnings as shown above.

        $$$$$$ Running command `terraform apply -lock=true -lock-timeout=0s -input=false -auto-approve=true  -parallelism=10 -refresh=true  -var-file="/        Users/orlando/GITHUB/terraform/k
        itchen-tutorial/tf_aws_cluster/testing.tfvars"` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/tf_aws_cluster
               aws_instance.example: Refreshing state... [id=i-0326bfc422beab45a]
               aws_instance.example: Creating...
               aws_instance.example: Still creating... [10s elapsed]
               aws_instance.example: Still creating... [20s elapsed]
               aws_instance.example: Creation complete after 27s [id=i-0084ed63993125567]

               Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

               Outputs:

               public_dns = ec2-3-88-10-176.compute-1.amazonaws.com
               Finished converging <default-ubuntu> (0m39.29s).
        -----> Kitchen is finished. (0m40.90s)
        ```

- And then run verify:
  
    ```bash
    bundle exec kitchen verify
    ```

    - it should show similar result to:

    ```bash
        ➜  tf_aws_cluster git:(master) bundle exec kitchen verify
    -----> Starting Kitchen (v1.25.0)
    -----> Setting up <default-ubuntu>...
           Finished setting up <default-ubuntu> (0m0.00s).
    -----> Verifying <default-ubuntu>...
    $$$$$$ Running command `terraform workspace select kitchen-terraform-default-ubuntu` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/ tf_aws_cluster
    $$$$$$ Running command `terraform output -json` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/tf_aws_cluster
           Finished verifying <default-ubuntu> (0m0.11s).
    -----> Kitchen is finished. (0m1.74s)
    ➜  tf_aws_cluster git:(master) 
    ```

- Now destroy your test instances

    ```bash
    bundle exec kitchen destroy
    ```

    - it should show similar result to:
  
    ```bash
    ➜  tf_aws_cluster git:(master) ✗ bundle exec kitchen destroy  
    -----> Starting Kitchen (v1.25.0)
    -----> Destroying <default-ubuntu>...
           Terraform v0.12.16
           + provider.aws v2.39.0
    $$$$$$ Running command `terraform init -input=false -lock=true -lock-timeout=0s  -force-copy -backend=true  -get=true -get-plugins=true     -verify-plugins=true` in directory /User
    s/orlando/GITHUB/terraform/kitchen-tutorial/tf_aws_cluster

           Initializing the backend...

           Initializing provider plugins...

           The following providers do not have any version constraints in configuration,
           so the latest version was installed.

           To prevent automatic upgrades to new major versions that may contain breaking
           changes, it is recommended to add version = "..." constraints to the
           corresponding provider blocks in configuration, with the constraint strings
           suggested below.

           * provider.aws: version = "~> 2.39"

           Terraform has been successfully initialized!
    $$$$$$ Running command `terraform workspace select kitchen-terraform-default-ubuntu` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/ tf_aws_cluster
    $$$$$$ Running command `terraform destroy -auto-approve -lock=true -lock-timeout=0s -input=false  -parallelism=10 -refresh=true  -var-file="/Users/ orlando/GITHUB/terraform/kitc
    hen-tutorial/tf_aws_cluster/testing.tfvars"` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/tf_aws_cluster
           aws_instance.example: Refreshing state... [id=i-0084ed63993125567]
           aws_instance.example: Destroying... [id=i-0084ed63993125567]
           aws_instance.example: Still destroying... [id=i-0084ed63993125567, 10s elapsed]
           aws_instance.example: Still destroying... [id=i-0084ed63993125567, 20s elapsed]
           aws_instance.example: Still destroying... [id=i-0084ed63993125567, 30s elapsed]
           aws_instance.example: Destruction complete after 32s

           Destroy complete! Resources: 1 destroyed.
    $$$$$$ Running command `terraform workspace select default` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/tf_aws_cluster
           Switched to workspace "default".
    $$$$$$ Running command `terraform workspace delete kitchen-terraform-default-ubuntu` in directory /Users/orlando/GITHUB/terraform/kitchen-tutorial/ tf_aws_cluster
           Deleted workspace "kitchen-terraform-default-ubuntu"!
           Finished destroying <default-ubuntu> (0m44.43s).
    -----> Kitchen is finished. (0m46.07s)
    ```