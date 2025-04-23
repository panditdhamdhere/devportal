---
sidebar_label: Debugging and Troubleshooting
sidebar_position: 106
title:  Common Errors and Tips
description: "Learn about some potential issues you can run into and tips on how to resolve them."
tags: [guides, developers, smart contracts, rsk, rootstock, foundry, dApps, ethers]
---

This section provides help on some potential issues you may run into and tips on how to resolve them.

## Errors

````mdx-code-block
<Accordion>
  <Accordion.Item eventKey="0">
    <Accordion.Header as="h3">Error: Transaction dropped from the mempool or Error Transaction not completed</Accordion.Header>
    <Accordion.Body>
      Check the `tx-id` in the explorer. The tx may have went successful but the error is still in the logs. Here are the [mainnet](https://explorer.rootstock.io/) and [testnet](https://explorer.testnet.rootstock.io/) explorers.
    </Accordion.Body>
  </Accordion.Item>
  <Accordion.Item eventKey="1">
    <Accordion.Header as="h3">Error Failed to get EIP-1559 fees</Accordion.Header>
    <Accordion.Body>
      - EIP-1559 is not supported or not activated on the Rootstock RPC url. The `--legacy` flag is passed to use legacy transactions instead of `EIP-1559`.
    </Accordion.Body>
  </Accordion.Item>
</Accordion>
````
