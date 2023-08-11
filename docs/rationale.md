---
nav_order: 2
---

# Rationale

Ignition is a distribution-agnostic provisioning utility. When a machine boots for the first time, Ignition will manipulate it in various ways to set up the machine for operations.

There are several principles that have guided Ignition's design. This document will explain the principles used, and the impact they have had on Ignition's design.

## Ignition runs only on the first boot

Ignition is designed to be used as a provisioning tool, not as a configuration management tool. Ignition encourages immutable infrastructure, in which machine modification requires that users discard the old node and re-provision the machine. This maintains the user's machines in a well known state with relatively simple tooling. (Ignition can be used to set up configuration management tools, if required, but that is not the best use of this utility.)

To this end, Ignition runs on the first boot of a machine. Being a general provisioning utility however, there's nothing stopping a user from using Ignition to set up some configuration management tool. This may even make a lot of sense depending on the organization and requirements the user is trying to meet.

From the user's perspective this first boot is not special in any way. The system is fully provisioned and ready for use once fully booted.

## Ignition produces the machine specified or no machine at all

Ignition does what it needs to make the system match the state described in the Ignition config. If for any reason Ignition cannot deliver the exact machine that the config asked for, Ignition prevents the machine from booting successfully.

For example, if the user wanted to fetch the document hosted at `https://example.com/foo.conf` and write it to disk, Ignition would prevent the machine from booting if it were unable to resolve the given URL.

## Ignition configs are declarative

Ignition configs describe the state of a system. Ignition configs do _not_ list a series of steps that Ignition should take.

Ignition configs do not allow users to provide arbitrary logic (including scripts for Ignition to run). Users describe which filesystems must exist, which files must be created, which users must exist, and etc. Any further customization must use systemd services, created by Ignition.

Ignition does not guess how the system should be configured.  For example, it does not automatically select a disk to hold any requested partitions.  Users must specify the configuration explicitly.

## Ignition configs should not be written by hand

Ignition configs were designed to be human readable, but difficult to write, to discourage users from attempting to write configs by hand.  Ignition configs support basic primitives such as files, directories, filesystems, and partitions, but do not provide higher-level functionality for configuring specific system services.

Use [Butane](https://coreos.github.io/butane/), or a similar tool, to generate Ignition configs. Butane reads [Butane configs](https://coreos.github.io/butane/specs/), which are YAML files containing distribution-independent Ignition directives and optional distribution-specific directives that provide an easier way to perform certain configuration tasks. Butane validates a Butane config for common errors and produces an Ignition config ready to be passed to a machine.

## Ignition is distro-independent

Ignition provides functionality that is broadly applicable to image-based Linux distributions.  It does not provide distro-specific functionality such as package management.
