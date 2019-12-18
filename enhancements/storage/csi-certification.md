---
title: csi-certification
authors:
  - "@fbertina"
reviewers:
  - "jsafrane"
  - "@gnufied”
  - "@chuffman"
approvers:
  - "@..."
creation-date: 2019-12-18
last-updated: 2019-12-18
status: provisional
see-also:
replaces:
superseded-by:
---

# CSI Certification

## Release Signoff Checklist

- [ ] Enhancement is `implementable`
- [ ] Design details are appropriately documented from clear requirements
- [ ] Test plan is defined
- [ ] Graduation criteria for dev preview, tech preview, GA
- [ ] User-facing documentation is created in [openshift-docs](https://github.com/openshift/openshift-docs/)

## Summary

We want to have a CSI certification suite that our storage vendors can use to certify their CSI drivers.

## Motivation

As storage vendors want to include their solutions into the OpenShift ecosystem, they will need to make their CSI drivers installable via an operator. To get support for their drivers, they need to certify them and we need to provide a way to test that the driver they provide meets the key requirements.

### Goals

The goal is to allow a storage vendors to be able to run a test that validates their CSI driver for use with OpenShift as a step in the process to certify their operators on the OpenShift Operator Hub.

### Non-Goals

* Validate CSI driver operators, this proposal targets CSI drivers only.
* Troubleshoot or debug CSI drivers. This is the responsibility of the storage vendor.

## Proposal

We propose that in order to be certified, the CSI driver should meet the following requirements:

* The driver should use the CSI sidecars shipped by Red Hat:
  * csi-external-provisioner: https://github.com/openshift/csi-external-provisioner
  * csi-external-attacher: https://github.com/openshift/csi-external-attacher
  * csi-external-snapshotter: https://github.com/openshift/csi-external-snapshotter
  * csi-external-resizer: https://github.com/openshift/csi-external-resizer
  * csi-node-driver-registrar: https://github.com/openshift/csi-node-driver-registrar

* The driver should pass all tests contained in the `openshift/csi` test suite executed by the `openshift-tests` utility.
  * The cluster should not have custom settings aiming the tests to pass, like SELinux disabled.
  * The CSI driver should be installed in the cluster. This should ideally be performed via an operator.
  * A manifest file should be created for the CSI driver.
	* This file should describe the supported features, so that the `openshift-tests` utility knows which tests should run.
	* Here is the [upstream documentation](https://github.com/kubernetes/kubernetes/blob/master/test/e2e/storage/external/README.md) that describe its format.
  * Set the location of the manifest file through the environment variable `TEST_CSI_FRIVER_FILES` and run the CSI test suite.
	* Example: `TEST_CSI_DRIVER_FILES=csi_driver_manifest.yaml openshift-tests run openshift/csi`
  * It should talk a while for all tests to be executed.
	* At the end, all tests should have passed.

### User Stories [optional]

#### Story 1

As a storage vendor, I want to make my CSI driver available to OCP users so that they can use my storage backend.

#### Story 2

As OCS user, I want to storage my application's data to a 3rd party storage backend and have confidence that it'll work reliably.

### Implementation Details/Notes/Constraints [optional]

No technical work needs to be done in the `openshift-tests` utility, as it already contains the `openshift/csi` test suite.

Since this utility is not currently shipped in a user-friendly way, we do need a strategy for that.

### Risks and Mitigations

## Design Details

### Test Plan

We want the `openshift/csi` test suite running in our CI for at least one CSI driver, possibly the EBS driver. This CSI job should be using CSI sidecars shipped by Red Hat.

### Graduation Criteria

There is no dev-preview phase.

##### Tech Preview

* The `openshift/csi` test suite running against a CSI driver in our CI.
* The `openshift-tests` utility is available to storage vendors.
* Documentation on how storage vendors could run the test suite and certify their drivers.

##### Removing a deprecated feature

### Upgrade / Downgrade Strategy

### Version Skew Strategy

## Implementation History

## Drawbacks

## Alternatives

## Infrastructure Needed [optional]
