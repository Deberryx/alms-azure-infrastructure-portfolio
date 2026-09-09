# Cost & Architecture Trade-offs

## Planning constraint

The staging architecture was designed against an approximate **$70/month ceiling** rather than treating cost as an afterthought.

The documented planning estimate was:

- Linux `Standard_B2s` VM: about $39.57/month;
- 64 GiB Standard SSD OS disk: about $7.01/month;
- Standard static IPv4 for explicit outbound: about $3.65/month;
- Cool LRS backup storage and light operations: roughly $0.50–$1.50/month;
- ordinary diagnostics/bandwidth allowance: roughly $1–$3/month;
- SQL Server 2022 Express: $0 license cost;
- Cloudflare Tunnel: $0 Azure charge.

This produced a base planning estimate around **$52–55/month**. Optional limited observability was expected to keep the design around **$60–65/month** until measured against actual regional ingestion and usage.

## NAT Gateway decision

A NAT Gateway was deliberately not included in the initial budget because its fixed hourly cost would consume much of the remaining allowance. The architecture instead prepared explicit outbound connectivity through a Standard Public IP attached to the VM NIC while denying unsolicited application ingress through the NSG.

If policy prohibits a VM public IP, the design requires a different approved egress architecture and a new cost review.

## Other cost controls

- SQL Server Express selected only while workload size and performance remain appropriate.
- Storage uses LRS and lifecycle controls rather than pretending to provide regional DR.
- Optional observability is capped and disabled until budget ownership is configured.
- Budget alerts are part of the Bicep design but require real contacts and approved dates before activation.

## Engineering lesson

Cost optimization is not the same as choosing the cheapest component. The design records where lower cost creates limitations in availability, recovery, scaling or security so that future upgrades have explicit triggers.
