# Terraform Provider for Statuspage.io

The Statuspage provider is used to interact with the resources supported by Statuspage.io.
Authentication currently works by setting the environment variable STATUSPAGE_TOKEN


## Download

You can download the [latest build from Gitlab](https://gitlab.com/yannhamon/terraform-provider-statuspage/-/jobs/artifacts/master/download?job=build)


## statuspage_component

Components are the individual pieces of infrastructure that are listed on your status page.

### Example usage

```
resource "statuspage_component" "my_component" {
    page_id     = "pageid"
    name        = "My Website"
    description = "Status of my website"
    status      = "operational"
}
```

### Argument Reference

The following arguments are supported:

 * page_id - (Required) the id of the page this component belongs to
 * name - (Required) Name of the component
 * description - Description of the component
 * status - status of the component - must be one of "operational", "under_maintenance", "degraded_performance", "partial_outage", "major_outage" or ""
 * only_show_if_degraded (bool) - Requires a special feature flag to be enabled
 * showcase (bool) - Should this component be showcased

## statuspage_component_group

Component groups provide a way to organize components. When a group is deleted, its child components will be orphaned. Note: A group cannot be empty, so if all the child components are deleted, the group will be deleted automatically. Another implication of this is that components must be created before their groups, when a group is created it will require a list of component IDs.

### Example usage

```
resource "statuspage_component_group" "my_group" {
    page_id     = "pageid"
    name        = "terraform"
    description = "Created by terraform"
    components  = ["${statuspage_component.my_component.id}"]
}
```

## statuspage_metrics

System metrics are a great way to build trust and transparency around your organization, and ensure that your page is doing work for you each and every day.

### Example usage

```
resource "statuspage_metric" "website_metrics" {
    page_id            = "pageid"
    metric_provider_id = "${statuspage_metrics_provider.statuspage_pingdom.id}"
    name               = "My Website"
    metric_identifier  = "pingdom_check_id"
}
```

## statuspage_metrics_provider

### Example usage

```
resource "statuspage_metrics_provider" "statuspage_pingdom" {
    page_id         = "pageid"
    email           = "myemail@provider.com"
    password        = "pingdom_password"
    application_key = "pingdomAppKey"
    type            = "Pingdom"
}
```

## Requirements

- [Terraform](https://www.terraform.io/downloads.html) 0.10.x
- [Go](https://golang.org/doc/install) 1.11 (to build the provider plugin)

## Building The Provider

Clone repository to: `$GOPATH/src/github.com/yannh/terraform-provider-statuspage

```sh
$ mkdir -p $GOPATH/src/github.com/yannh; cd $GOPATH/src/github.com/yannh
$ git clone git@github.com:yannh/terraform-provider-statuspage
```

Enter the provider directory and build the provider

```sh
$ cd $GOPATH/src/github.com/yannh/terraform-provider-statuspage
$ make build
```

## Developing the Provider

If you wish to work on the provider, you'll first need [Go](http://www.golang.org) installed on your machine (version 1.11+ is *required*). You'll also need to correctly setup a [GOPATH](http://golang.org/doc/code.html#GOPATH), as well as adding `$GOPATH/bin` to your `$PATH`.

To compile the provider, run `make build`. This will build the provider and put the provider binary in the `$GOPATH/bin` directory.

```sh
$ make build
...
$ $GOPATH/bin/terraform-provider-statuspage
...
```

In order to test the provider, you can simply run `make test`.

```sh
$ make test
```