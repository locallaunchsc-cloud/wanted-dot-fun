<img width="2400" height="1350" alt="a41baa11-622a-4292-b1aa-480e8ebef077" src="https://github.com/user-attachments/assets/24384b0b-5a27-4850-8783-424c6b1912e0" />

<h1 align="center">WANTED.FUN</h1>
<p align="center"><b>The memecoin launchpad where the chart earns the heat.</b><br>Climb the curve, earn wanted stars, graduate at three. Dump it and the cops lose you.</p>

<p align="center">
<img alt="status: prototype" src="https://img.shields.io/badge/status-prototype-orange">
<img alt="data: simulated" src="https://img.shields.io/badge/data-simulated-blue">
<img alt="engine: JavaScript" src="https://img.shields.io/badge/engine-JavaScript-yellow">
<img alt="node 18+" src="https://img.shields.io/badge/node-18%2B-green">
<img alt="license: MIT" src="https://img.shields.io/badge/license-MIT-lightgrey">
</p>

<p align="center"><a href="https://locallaunchsc-cloud.github.io/wanted-dot-fun/">Website</a> · <a href="#installation">Install</a> · <a href="#how-it-works">How it works</a> · <a href="#cli-reference">CLI reference</a> · <a href="#faq">FAQ</a></p>

<p align="center"><img src="assets/home.png" alt="WANTED.FUN home page" width="820"></p>

<p align="center"><a href="#what-wanted-is">What WANTED is</a> · <a href="#the-problem">The problem</a> · <a href="#how-it-works">How it works</a> · <a href="#star-ladder">Star ladder</a> · <a href="#status">Status</a> · <a href="#quick-start">Quick start</a> · <a href="#javascript-sdk">SDK</a> · <a href="#cli-reference">CLI</a> · <a href="#configuration">Configuration</a> · <a href="#events">Events</a> · <a href="#roadmap">Roadmap</a> · <a href="#faq">FAQ</a> · <a href="#disclaimer">Disclaimer</a></p>

## What WANTED is

Memecoin launchpads show a price and a number. WANTED turns the chart into a game mechanic borrowed from the genre every GTA VI coin is riding: the wanted level.

Every coin launches with one star. As its market cap climbs it earns more heat. At three stars it graduates from the bonding curve to a DEX. At four it is Most Wanted. At five it is a citywide manhunt. If the chart dumps, the stars drop and the cops lose you.

| Half | What it is |
|---|---|
| **Surface** | The website: home, a live Most Wanted board, a launch form, docs and an animated star-up demo. |
| **Engine** | `wanted.js`: the star ladder, a simulated market and an event stream. One file, works in browsers and Node. |

<p align="center"><img src="assets/dip-3-star.png" alt="A coin graduating at three stars" width="820"></p>

## The problem

Launchpads all look the same, and a coin's progress is a bar nobody remembers.

| Plain launchpad | WANTED |
|---|---|
| Progress is a percentage bar | Progress is a star level people already understand |
| Graduation is a quiet status change | Graduation is a moment: sirens, banner, a clip-ready screen |
| A dump is just a red candle | A dump costs stars, so rugs and drawdowns are visible |
| Coins look interchangeable | Every coin has a heat level and a place on the Most Wanted board |

The star change is the product. Each one is a short, shareable moment that advertises the coin and the platform.

## How it works

Four layers, each independently testable.

1. **Ladder.** `TIERS` maps market cap to stars. One list, edited in one place.
2. **Hysteresis.** A star is only lost after market cap falls 10% below its threshold, so stars do not flicker around a boundary.
3. **Market.** A `Market` holds coins, advances them with `tick()` and emits events when stars change.
4. **Surface.** The pages subscribe to those events to draw the board, the feed and the star-up animation.

Today the market is a random-walk simulation. When devnet launches land, the same events will be driven by real curve progress instead.

## Star ladder

| Stars | Market cap | Meaning |
|---|---|---|
| ★ | $5,000 | Launched |
| ★★ | $25,000 | Gaining heat |
| ★★★ | $69,000 | Graduated: liquidity moves to a DEX |
| ★★★★ | $100,000 | Most Wanted |
| ★★★★★ | $500,000 | Citywide manhunt |

## Status

| Area | State |
|---|---|
| Star engine, simulated market, events | **Built** |
| Website: home, markets, launch form, docs, demo | **Built** |
| Command-line tool | **Built** |
| Devnet wallet connect and real launches | Planned |
| Indexer, REST feed and webhooks | Planned |
| Auto-rendered star-up clips | Planned |
| Python and Go bindings | Planned |

Everything on the site runs on simulated data. No wallets, contracts or real tokens are involved yet.

## Installation

There is nothing to install for the site.

| Goal | How |
|---|---|
| View the site | Open `index.html` in a browser, or visit the [live site](https://locallaunchsc-cloud.github.io/wanted-dot-fun/) |
| Use the CLI | Node 18+, then `node cli/wanted.js ladder` |
| Use the SDK in a page | `<script src="wanted.js"></script>` |
| Use the SDK in Node | `const Wanted = require("./wanted.js")` |
| Deploy your own copy | Fork, then Settings > Pages > Deploy from branch > `main` / root |

## Quick start

```bash
git clone https://github.com/locallaunchsc-cloud/wanted-dot-fun.git
cd wanted-dot-fun
node cli/wanted.js ladder
node cli/wanted.js stars 80000
node cli/wanted.js sim 200
```

In a page:

```html
<script src="wanted.js"></script>
<script>
  const m = new Wanted.Market();
  m.on("star_up", c => console.log(c.ticker, "now has", c.stars, "stars"));
  m.on("graduate", c => console.log(c.ticker, "bonded"));
  m.launch("Duck", "DUCK");
  setInterval(() => m.tick(), 1000);
</script>
```

## JavaScript SDK

| Call | Returns |
|---|---|
| `Wanted.TIERS` | The star ladder: `{stars, mc, label}` for each level |
| `Wanted.starsFor(mc, prev = 1)` | Star level for a market cap, holding `prev` through the 10% buffer |
| `Wanted.fmt(n)` | A number as US dollars, e.g. `$69,420` |
| `Wanted.loadUser()` / `Wanted.saveUser(list)` | Read and write the coins launched from the Launch page (browser storage) |
| `new Wanted.Market()` | A simulated market |
| `market.launch(name, ticker, mc = 5000)` | Adds a coin and returns it |
| `market.tick()` | Advances every coin one step |
| `market.on(event, fn)` | Subscribes to an event |
| `market.coins` | The list of coins: `{name, ticker, mc, stars, graduated, hist}` |

## CLI reference

| Command | Description |
|---|---|
| `node cli/wanted.js ladder` | Print the star ladder |
| `node cli/wanted.js stars <marketcap>` | Print the star level for a market cap |
| `node cli/wanted.js sim [ticks]` | Run the simulated market and print star events |

## Configuration

These live at the top of `wanted.js`. The site and `demo.html` both read from there, so a change applies everywhere.

| Name | Effect |
|---|---|
| `TIERS` | Market cap needed for each star and its label |
| `DECAY` | Fraction of a threshold a coin must fall below to lose that star. Default `0.9` |
| `DEMO` | The seeded coins on the Markets board |

## Events

| Event | Fired when |
|---|---|
| `launch` | A coin is added to the market |
| `star_up` | A coin earns a star |
| `star_down` | A coin loses a star |
| `graduate` | A coin reaches three stars for the first time |

## Project layout

| Path | What it is |
|---|---|
| `index.html` | Home: hero, live countdown to the US unlock, top coins, ladder |
| `demo.html` | Animated star-up demo with Pump, Dump and Auto controls |
| `markets.html` | Live Most Wanted board and activity feed |
| `launch.html` | Simulated launch form, saved in your browser only |
| `docs.html` | Docs, each item marked Built or Planned |
| `wanted.js` | Star engine, market and countdown |
| `cli/wanted.js` | Command-line tool |
| `style.css` | Shared styles |
| `assets/` | Screenshots used in this README |

## Roadmap

| State | Item |
|---|---|
| done | Star engine, simulated market and events |
| done | Website, demo and CLI |
| next | Devnet wallet connect and launch flow on an existing bonding-curve SDK |
| next | Indexer that maps real curve progress to stars |
| later | REST feed and webhooks for star changes |
| later | Auto-rendered star-up clips for X and TikTok |
| later | Python and Go bindings |
| later | Custom curve and audit, only after traction |

## FAQ

**Does this run inside GTA VI?**
No. WANTED is a website. It borrows the wanted-level idea and the look of the game's world. It does not touch the game, a console or any game files.

**Are these real coins?**
No. The markets are simulated and launches are saved in your browser only. No wallet is connected and nothing goes on-chain.

**When do real launches arrive?**
They are on the roadmap, starting with devnet. This README and the docs mark what is built and what is planned.

**Can I build on it?**
Yes. The engine is one MIT-licensed file with a small event API. Open an issue with what you want to see.

**Why simulated data?**
So every claim on the site is true today, and so the demo works without a wallet.

## Disclaimer

WANTED.FUN is an unofficial parody project. It is not affiliated with, endorsed by or sponsored by Rockstar Games or Take-Two Interactive. Grand Theft Auto is a trademark of its respective owner. Nothing here is financial advice. Running a launchpad that takes fees can carry legal and compliance exposure; get advice before handling real tokens or money.

## Contributing

Issues and pull requests are welcome.

## License

MIT
