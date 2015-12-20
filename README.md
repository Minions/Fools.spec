# Fools.spec
This is the specification for the Fools mini-language. If your runtime-specific version of Fools passes this spec, then all of Minions (including all tools) will run on it.

## Components

A valid Fools implementation contains the following pieces.

* **Giberish**: language services. Code transformations service. Can compile, refactor, and manage history for chunks of Fools code.
* **Runtime**: The core VM. Provides runtime constructs to execute code. These are Fools, Secret Messages, Secret Locations, Loot, Cities, and Thunks.
* **Platform**: Exposes platform services in a uniform way to Fools code.

Some parts of these components are provided directly by the implementation. Some are written in Fools and compiled into the implementation.

This spec verifies all 3 components. The spec itself was built alongside the first implementation, in a TDD fashion. Each commit adds one or two more tests for some part of the system. Thus the history of this project is a not bad sequence of stories for implementing Fools.

The spec is written in a combination of Fools and Fasm (fools assembly language). Fasm is like a trivial subset of Fools that is easier to parse. It includes a few awkward-for-humans constructs to make compilation easier. That's why it isn't a strict subset of Fools. But Fasm is designed such that the parser for Fools and Fasm should be very similar / re-usable.

When building a new implementation, start with the oldest version of the spec and then go forward one version at a time. That way you always know your level of compliance with the whole.

The spec will also ensure that you are claiming the correct level of compliance (core version + capabilities and their versions), which means Minions will be able back-compat itself to run as much as possible on your Fools implementation. You should see basic Minions functionality start to appear fairly quickly as you implement Fools.

Git tags make it easy to see where in history certain capabilities will appear.

## Usage

Build and execute the Fools Spec project to run your tests. In all versions, executing the project will run the entire then-current test suite over your implementation and give test results.

This will be a very simple test tool in early versions. It evolves along with the Fools spec & implementation, taking advantage of new language capabilities after they have come online.

Initially it is unspecified how you build and execute. Eventually a build system will be built and introduced. At that point you will execute the test by running the project's default target.

## Bootstrap requirements

The bootstrapped version of this project is not actually written in Fools, or even fasm (the fools assembly language). Instead, it is a set of fragments to compile and assertions about the compiled code. TDD your compiler by running it over the fragments and making sure you meet the assertion. Each test consists of 2 or 3 files. These are as follows:

* **[test\_name.fool]:** the source for the test. Compile this to get a city in memory.
* **[test\_name.compiler\_messages]:** a list of messages your compiler must send (errors and warnings) when attempting to compile this test. If missing, then your compiler must send no messages. Your compiler must not send any messages other than those mentioned.
* **[test\_name.assertions.fool]:** assertions that you can make. Each assertion consists of a message to send and expected messages that will then be sent. Your test engine should send each message and make sure exactly the expected messages are sent. The assertions file is valid fool code; you can compile it and execute the thunk if you would like. Or you can write a simple special-purpose parser if you don't want to handle as much of the language. The only assertions used will be `assert.thunk.will.send`.

`assert.thunk.will.send(thunk.address, initial.message, {result.sends})` is defined as:

1. Subscribe to all messages sent by processors at `thunk.address` for processing of a single message.
2. Send `initial.message` to `thunk.address`
3. Cancel propagation of any messages to any named addresses.
4. Verify that the set of messages sent equals the set `{result.sends}`. As in, exactly the same elements, based on send equality.
	* A send is the type {sent: Message, to: Maybe<Address>}.

This is parallel-safe as long as the first 2 steps are performed as a single operation. Your message queue should support a `send.and.subscribe.to.results` operation, as this will be required eventually for a valid fools implementation.

`send.and.subscribe.to.results` will eventually be defined specifically in tests. That is the valid specification, but its semantics are:

* Given a `message` to process, an `address` to send it to, and a `sending.thunk` that invoked the operation:
	1. Create a subscription that will deliver all messages to the message receiver active at the send point of the `sending.thunk`.
	2. Subscribe that subscription to receive "all" messages sent during "full processing" of the message to be delivered.
		* All messages sent with no specific destination (Address is Maybe.not) are delivered directly to this listening point. This is the only way such messsages will be delivered anywhere.
		* All messages sent to a named destination are delivered first to this listening point, then delivered as normal to that address.
		* This listening point can choose to cancel propagation of messages to named addresses. Default is to propagate.
		* Full Processing means until after the message is fully done, including any asynchronous portions of processing, but not including processing of any messages that might be sent as a result of processing this message.
	3. Clean up when done. Unsubscribe any listeners.
	4. Like a normal send, execution continues at the caller immediately. It does not block on the sent message being processed.

## License

The Fools spec is Copyright &copy; The Minions Project, 2015. It is licensed under the 3-clause BSD license.
