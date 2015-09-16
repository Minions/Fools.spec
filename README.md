# Fools.spec
This is the specification for the Fools mini-language. If your runtime-specific version of Fools passes this spec, then all of Minions (including all tools) will run on it.

## Components

A valid Fools implementation contains the following pieces.

* **Giberish**: language services. Code transformations service. Can compile, refactor, and manage history for chunks of Fools code.
* **Runtime**: The core VM. Provides runtime constructs to execute code. These are Fools, Secret Messages, Loot, and Thunks.
* **Platform**: Exposes platform services in a uniform way to Fools code.

This spec verifies all 3. The spec itself was built alongside the first implementation, in a TDD fashion. Each commit adds one or two more tests for some part of the system. Thus the history of this project is a not bad sequence of stories for implementing Fools.

When building a new implementation, start with the oldest version of the spec and then go forward one version at a time. That way you always know your level of compliance with the whole.

The spec will also ensure that you are claiming the correct level of compliance (core version + capabilities and their versions), which means Minions will be able back-compat itself to run as much as possible on your Fools implementation. You should see basic Minions functionality start to appear fairly quickly as you implement Fools.

Git tags make it easy to see where in history certain capabilities will appear.

## Usage

Execute the Fools Spec project to run your tests. In all versions, executing the project will run the entire then-current test suite over your implementation and give test results.

This will be a very simple test tool in early versions. It evolves along with the Fools spec & implementation, taking advantage of new language capabilities after they have come online.

## License

The Fools spec is Copyright &copy; The Minions Project, 2015. It is licensed under the 3-clause BSD license.
