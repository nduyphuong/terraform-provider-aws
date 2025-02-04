---
subcategory: "VPC (Virtual Private Cloud)"
layout: "aws"
page_title: "AWS: aws_vpc_peering_connection_accepter"
description: |-
  Manage the accepter's side of a VPC Peering Connection.
---


<!-- Please do not edit this file, it is generated. -->
# Resource: aws_vpc_peering_connection_accepter

Provides a resource to manage the accepter's side of a VPC Peering Connection.

When a cross-account (requester's AWS account differs from the accepter's AWS account) or an inter-region
VPC Peering Connection is created, a VPC Peering Connection resource is automatically created in the
accepter's account.
The requester can use the `awsVpcPeeringConnection` resource to manage its side of the connection
and the accepter can use the `awsVpcPeeringConnectionAccepter` resource to "adopt" its side of the
connection into management.

## Example Usage

```typescript
// Code generated by 'cdktf convert' - Please report bugs at https://cdk.tf/bug
import { Construct } from "constructs";
import { Token, TerraformStack } from "cdktf";
/*
 * Provider bindings are generated by running `cdktf get`.
 * See https://cdk.tf/provider-generation for more details.
 */
import { DataAwsCallerIdentity } from "./.gen/providers/aws/data-aws-caller-identity";
import { AwsProvider } from "./.gen/providers/aws/provider";
import { Vpc } from "./.gen/providers/aws/vpc";
import { VpcPeeringConnection } from "./.gen/providers/aws/vpc-peering-connection";
import { VpcPeeringConnectionAccepterA } from "./.gen/providers/aws/vpc-peering-connection-accepter";
class MyConvertedCode extends TerraformStack {
  constructor(scope: Construct, name: string) {
    super(scope, name);
    new AwsProvider(this, "aws", {
      region: "us-east-1",
    });
    const peer = new AwsProvider(this, "aws_1", {
      alias: "peer",
      region: "us-west-2",
    });
    const main = new Vpc(this, "main", {
      cidrBlock: "10.0.0.0/16",
    });
    const awsVpcPeer = new Vpc(this, "peer", {
      cidrBlock: "10.1.0.0/16",
      provider: peer,
    });
    const dataAwsCallerIdentityPeer = new DataAwsCallerIdentity(
      this,
      "peer_4",
      {
        provider: peer,
      }
    );
    /*This allows the Terraform resource name to match the original name. You can remove the call if you don't need them to match.*/
    dataAwsCallerIdentityPeer.overrideLogicalId("peer");
    const awsVpcPeeringConnectionPeer = new VpcPeeringConnection(
      this,
      "peer_5",
      {
        autoAccept: false,
        peerOwnerId: Token.asString(dataAwsCallerIdentityPeer.accountId),
        peerRegion: "us-west-2",
        peerVpcId: Token.asString(awsVpcPeer.id),
        tags: {
          Side: "Requester",
        },
        vpcId: main.id,
      }
    );
    /*This allows the Terraform resource name to match the original name. You can remove the call if you don't need them to match.*/
    awsVpcPeeringConnectionPeer.overrideLogicalId("peer");
    const awsVpcPeeringConnectionAccepterPeer =
      new VpcPeeringConnectionAccepterA(this, "peer_6", {
        autoAccept: true,
        provider: peer,
        tags: {
          Side: "Accepter",
        },
        vpcPeeringConnectionId: Token.asString(awsVpcPeeringConnectionPeer.id),
      });
    /*This allows the Terraform resource name to match the original name. You can remove the call if you don't need them to match.*/
    awsVpcPeeringConnectionAccepterPeer.overrideLogicalId("peer");
  }
}

```

## Argument Reference

The following arguments are supported:

* `vpcPeeringConnectionId` - (Required) The VPC Peering Connection ID to manage.
* `autoAccept` - (Optional) Whether or not to accept the peering request. Defaults to `false`.
* `tags` - (Optional) A map of tags to assign to the resource. If configured with a provider [`defaultTags` configuration block](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#default_tags-configuration-block) present, tags with matching keys will overwrite those defined at the provider-level.

### Removing `awsVpcPeeringConnectionAccepter` from your configuration

AWS allows a cross-account VPC Peering Connection to be deleted from either the requester's or accepter's side.
However, Terraform only allows the VPC Peering Connection to be deleted from the requester's side
by removing the corresponding `awsVpcPeeringConnection` resource from your configuration.
Removing a `awsVpcPeeringConnectionAccepter` resource from your configuration will remove it
from your statefile and management, **but will not destroy the VPC Peering Connection.**

## Attributes Reference

In addition to all arguments above, the following attributes are exported:

* `id` - The ID of the VPC Peering Connection.
* `acceptStatus` - The status of the VPC Peering Connection request.
* `vpcId` - The ID of the accepter VPC.
* `peerVpcId` - The ID of the requester VPC.
* `peerOwnerId` - The AWS account ID of the owner of the requester VPC.
* `peerRegion` - The region of the accepter VPC.
* `accepter` - A configuration block that describes [VPC Peering Connection]
(https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html) options set for the accepter VPC.
* `requester` - A configuration block that describes [VPC Peering Connection]
(https://docs.aws.amazon.com/vpc/latest/peering/what-is-vpc-peering.html) options set for the requester VPC.
* `tagsAll` - A map of tags assigned to the resource, including those inherited from the provider [`defaultTags` configuration block](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#default_tags-configuration-block).

#### Accepter and Requester Attributes Reference

* `allowRemoteVpcDnsResolution` - Indicates whether a local VPC can resolve public DNS hostnames to
private IP addresses when queried from instances in a peer VPC.

## Import

VPC Peering Connection Accepters can be imported by using the Peering Connection ID, e.g.,

```sh
$ terraform import aws_vpc_peering_connection_accepter.example pcx-12345678
```

Certain resource arguments, like `autoAccept`, do not have an EC2 API method for reading the information after peering connection creation. If the argument is set in the Terraform configuration on an imported resource, Terraform will always show a difference. To workaround this behavior, either omit the argument from the Terraform configuration or use [`ignoreChanges`](https://www.terraform.io/docs/configuration/meta-arguments/lifecycle.html#ignore_changes) to hide the difference, e.g.,

```typescript
// Code generated by 'cdktf convert' - Please report bugs at https://cdk.tf/bug
import { Construct } from "constructs";
import { TerraformStack } from "cdktf";
/*
 * Provider bindings are generated by running `cdktf get`.
 * See https://cdk.tf/provider-generation for more details.
 */
import { VpcPeeringConnectionAccepterA } from "./.gen/providers/aws/vpc-peering-connection-accepter";
interface MyConfig {
  vpcPeeringConnectionId: any;
}
class MyConvertedCode extends TerraformStack {
  constructor(scope: Construct, name: string, config: MyConfig) {
    super(scope, name);
    new VpcPeeringConnectionAccepterA(this, "example", {
      lifecycle: {
        ignoreChanges: [autoAccept],
      },
      vpcPeeringConnectionId: config.vpcPeeringConnectionId,
    });
  }
}

```

<!-- cache-key: cdktf-0.17.1 input-0e973382c1a14daed2d9feea9a490b8b12227e574b67fc6ba9380f629bb44356 -->