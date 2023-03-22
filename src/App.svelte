<script>
  import {
    DeployPlugin,
    InjectedArweaveSigner,
    // @ts-ignore
  } from "https://unpkg.com/warp-contracts-plugin-deploy@1.0.1/bundles/web.bundle.min.js";
  let arweave, warp;
  const contractSrc = "x0ojRwrcHBmZP20Y4SY0mgusMRx-IYTjg5W8c3UFoNs";

  async function postNote(e) {
    // @ts-ignore
    arweave = window.Arweave.init({
      host: "g8way.io",
      port: 443,
      protocol: "https",
    });
    // @ts-ignore
    warp = window.warp.WarpFactory.forMainnet().use(new DeployPlugin());
    //console.log(window.arweaveWallet);
    // @ts-ignore
    if (window.arweaveWallet) {
      // @ts-ignore
      await window.arweaveWallet.connect([
        "ACCESS_ADDRESS",
        "SIGN_TRANSACTION",
        "ACCESS_PUBLIC_KEY",
        "SIGNATURE",
        "DISPATCH",
      ]);
    }
    // @ts-ignore
    const address = await window.arweaveWallet.getActiveAddress();
    const tx = await arweave.createTransaction({ data: e.target.body.value });
    tx.addTag("Content-Type", "text/plain");
    tx.addTag("App-Name", "PublicSquare");
    tx.addTag("Version", "1.0.1");
    tx.addTag("Type", "post");
    // Atomic Token
    tx.addTag("App-Name", "SmartWeaveContract");
    tx.addTag("App-Version", "0.3.0");
    tx.addTag("Contract-Src", contractSrc);
    tx.addTag(
      "Init-State",
      JSON.stringify({
        pairs: [],
        balances: {
          [address]: 1 * 1e12,
        },
        name: "Atomic Token",
        ticker: "ATOMIC",
        settings: [["isTradeable", true]],
      })
    );
    // @ts-ignore
    const result = await window.arweaveWallet.dispatch(tx);
    await warp.register(result.id, "node2");
    alert("Successfully Deployed and Registered Post");
    console.log(result);
  }
</script>

<svelte:head>
  <link
    href="https://cdn.jsdelivr.net/npm/daisyui@2.51.5/dist/full.css"
    rel="stylesheet"
    type="text/css"
  />
  <script src="https://cdn.tailwindcss.com"></script>
  <script
    src="https://unpkg.com/browse/arweave@1.13.3/bundles/web.bundle.min.js"
  ></script>
  <script
    src="https://unpkg.com/warp-contracts@1.3.0/bundles/web.iife.bundle.min.js"
  ></script>
</svelte:head>

<h1 class="mb-16 text-2xl">Post to PublicSquare</h1>

<form class="flex flex-col space-y-4" on:submit|preventDefault={postNote}>
  <textarea class="textarea" name="body" />
  <button class="btn">Post</button>
</form>
