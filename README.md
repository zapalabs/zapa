# z-sno

z-sno is a project for integrating a zcash virtual machine into a custom Avalanche subnet.

This repo specifically is meant to make developing and debugging this project with VS Code on mac easy. With this repo, you can:

- Build and debug the official zcash node and cli.
- Build and debug the WIP zcashvm which is an avalanche virtual machine. 

After cloning, be sure to fetch submodules:

`git submodule update --init --recursive`

## Building and debugging zcash

- See [zcash/docs/debugging.md](zcash/docs/debugging.md)

## Building and debugging zcashvm with ava-sim

1. See the `#Building` section of the [zcashvm README](https://github.com/rkass/zcashvm/tree/main#building)
2. See the `#Debugging with VS Code` section of the [ava-sim README.md](https://github.com/rkass/ava-sim/tree/master#debugging-with-vs-code)
