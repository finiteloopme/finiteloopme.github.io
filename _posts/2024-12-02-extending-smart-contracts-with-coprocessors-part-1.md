---
layout: post
title: "Extending Smart Contracts with Coprocessors: A Beginner's Guide (Part 1)"
published: true
comments: true
categories:
    - technical
tags:
    - web3
    - ai-agent
    - onchain
    - coprocessor
    - blockchain
---

# Extending Smart Contracts with Coprocessors: A Beginner's Guide (Part 1)

*Originally published on [Medium](https://medium.com/@kunallimaye/extending-smart-contracts-with-coprocessors-a-beginners-guide-part-1-3af0547ea97c), 2 December 2024.*

The Ethereum Virtual Machine (EVM) has its limitations. Complex calculations can be costly and time-consuming, hindering the development of sophisticated decentralized applications.

Enter **coprocessors**, which offload computationally intensive tasks *off-chain*, while keeping the *on-chain* smart contracts lean and efficient. Coprocessors offer following advantages:

1. **Reduced Gas Costs:** Offloading complex computations reduces the amount of gas consumed on the Ethereum network, making your dApps more cost-effective.
2. **Improved Efficiency:** Coprocessors can perform calculations much faster than the Ethereum Virtual Machine (EVM), leading to improved performance and scalability.
3. **Enhanced Functionality:** With coprocessors, you can implement complex logic and algorithms that would be impossible or impractical to execute directly on the blockchain.

While *off-chain* processing with coprocessors offers numerous advantages, it's crucial to maintain security and integrity. We'll discuss how techniques like zero-knowledge proofs (ZKPs) and trusted execution environments (TEEs) can verify off-chain computations, ensuring the integrity of your dApps.

This first article provides a hands-on introduction to implementing basic off-chain computation using a coprocessor. Stay tuned for future installments where we delve deeper into the world of ZKPs and TEEs.

### Building an off-chain Image Processing Agent

Lets imagine a decentralised application which requires image processing. The program accepts image from a user and detects various objects in that image.

Instead of a smart contract performing computationally intensive image analysis, this task can be delegated to a processor. Here's how it works:

1. **The Smart Contract Emits an Event**: When a user uploads an image URL, the smart contract emits an event containing the image URL.
2. **The Coprocessor Listens for the Event**: Our off-chain coprocessor, written in Rust, continuously listens for these events.
3. **The Coprocessor Performs the Computation**: Upon receiving an event, the coprocessor downloads the image, performs the necessary analysis (e.g., object recognition, content filtering), and generates a result.
4. **The Coprocessor Calls Back the Smart Contract**: The coprocessor then calls a designated function in the smart contract to submit the result.

### Architecture Overview

In our example, *Image Analyser* uses Gemini to get a list of all the objects in the image. We could also deploy and use an open models like [Gemma](https://blog.google/technology/developers/gemma-open-models/) or [Llama](https://www.llama.com/docs/how-to-guides/).

In this example, *EventListener* is deployed alongside rest of the coprocessor components. An interesting pattern to explore is to decouple the *EventListener* and deploy on a *serverless* platform like Cloud Run or Cloud Functions. This would allow us to launch compute heavy components like *Image Analyser* on-demand.

### Sample Code

> Working code is on Github along with [the setup guide](https://github.com/finiteloopme/dcentral-labs/blob/coprocessor-part1/coprocessor/part1/README.md)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.13;

/// @title ImageProcessor
/// @notice A contract for uploading image URIs and storing analysis results
/// @dev This contract allows users to upload image URIs and stores results associated with each uploader
contract ImageProcessor {

    /// @notice Emitted when an image URI is uploaded
    /// @param imageUri The URI of the uploaded image
    /// @param uploader The address of the user who uploaded the image URI
    event ImageUploaded(string imageUri, address uploader);

    /// @notice Mapping to store analysis results for each uploader
    /// @dev Key is the uploader's address, value is the analysis result
    mapping(address => string) public results;

    /// @notice Uploads an image URI to the contract
    /// @param imageUri The URI of the image to be uploaded
    /// @dev Emits an ImageUploaded event
    function uploadImage(string memory imageUri) public {
        emit ImageUploaded(imageUri, msg.sender);
    }

    /// @notice Submits the analysis result for a specific uploader
    /// @param uploader The address of the user who uploaded the image URI
    /// @param result The analysis result for the uploaded image
    /// @dev This function should be called by an authorized coprocessor
    function submitResult(address uploader, string memory result) public {
        // Add authentication or authorization here to ensure only the
        // coprocessor can call this function
        results[uploader] = result;
    }
}
```

```rust
// Psuedocode ONLY, won't compile
//
// Main function to:
// 1. Connect to Eth RPC node
// 2. Listen for events & filter by ImageUploaded event
// 3. Analyse the image
// 4. Call submitResult on the smart contract
#[tokio::main]
async fn main() -> web3::Result<()> {
    // Connect to Eth node
    let web3 = Web3::new(WebSocket::new("URL_TO_RPC_NODE").await?); // Replace with your Ethereum node's WebSocket address

    // Build a filter for ImageUploaded event
    let filter = FilterBuilder::default()
        .address(vec![contract_address]) // Replace with your contract's address
        .topics(Some(vec![web3.eth().abi().event("ImageUploaded").signature()]), None, None, None)
        .build();

    // Listen for ImageUploaded events
    let mut sub = web3.eth_subscribe().subscribe_logs(filter).await?;

    // A helper function to load the contract instance
    let contract = get_signed_contract_instance(provider.clone(), contract_address);

    // Process each ImageUploaded event
    while let Some(log) = sub.next().await {
        let input = log.data.0[0].as_u64(); // Assuming the input is the first element in the data array
        let requester = log.topics[1]; // Assuming the requester's address is the second topic

        // Perform the computation
        let result = analyse_image(input);

        // Send the result back to the smart contract
        let submit_result_method = contract.submit_result(uploader, result);
    }

    Ok(())
}
```

### Conclusion

By offloading computationally intensive tasks to coprocessors, we can build more efficient, cost-effective, and feature-rich decentralized applications. This approach opens up a world of possibilities for developers, enabling them to create innovative solutions that push the boundaries of what's possible on the blockchain.

In the next part of this series, we'll explore how to enhance the privacy of our coprocessor interactions using ZKPs and TEEs. Stay tuned!

### References

1. [A Brief Intro to Coprocessors](https://crypto.mirror.xyz/BFqUfBNVZrqYau3Vz9WJ-BACw5FT3W30iUX3mPlKxtA) by *0xemperor.eth*
