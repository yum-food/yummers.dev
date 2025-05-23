---
pagetitle: yummers
---
## "big llms are memory bound"
22 May 2025

There is wisdom oft repeated that "big neural nets are memory bandwidth limited."
This is utter horseshit and I will show why.

LLMs are typically implemented as autoregressive feed-forward neural nets. This
means that to generate a sentence, you provide a *prompt* which the neural net
then uses to generate the next *token*. That prompt + token is fed back into
the neural net repeatedly until it produces an EOF token, marking the end of
generation.

We want to derive an equation predicting token rate `T`. Let's define some
variables:

```
T: token rate (tokens / second)
M: memory bandwidth (bytes / second)
P: model size (parameters)
C: compute throughput (parameters / second)
Q: model quantization (bytes / parameter)
```

Since each token requires accessing the entire model's weights, then on an
infinitely powerful computer:

```
T = M / (P * Q)
```

As the model size grows, our throughput drops; as memory bandwidth grows, our
throughput increases. Likewise, quantizing the model eases our memory pressure,
so reducing bytes/param increases our model rate. This is all expected.

However, most of our computers do not have infinite compute throughput. We must
then adjust our equation:

```
T = min(M / Q, C) / P
```

Our token rate increases until we saturate our compute `C` or our memory
bandwidth `M/Q`, then it stops. Totally reasonable.

Notably, *token rate uniformly drops as parameter count increases.* The common
wisdom that "big models are memory bound lol" is complete horseshit.

This equation helps you balance your compute against your memory bandwidth. You
can calculate your system's memory bandwidth as follows, assuming you have DDR5:

```
M_c: memory channels
M_s: memory speed (GT/s)

M = M_s * 8 * M_c
```

(Source: [wikipedia](https://en.wikipedia.org/wiki/DDR5_SDRAM))

So if you have 12 channels of DDR5 @ 6000 MT/s, that works out to
`12*8*6 = 576` GB/s.

Consider a model like [DeepSeek-V3-0324 in 2.42 bit quant](https://huggingface.co/unsloth/DeepSeek-V3-0324-GGUF).
This bad boy is a mixture of experts (MoE) with 37B activated parameters per
token. So at 2.42 bits / parameter, that works out to ~11.19 GB / token.
Assuming infinite compute, the upper bound on token generation rate is
`576 / 12.53 = 51.46` tokens / second.

I hate to be the bearer of bad news. You will not see this token rate. On my
shitass server with an
[EPYC 9135](https://www.amd.com/en/products/processors/server/epyc/9005-series/amd-epyc-9135.html)
CPU and 12 channels of ECC DDR5 @ 6000 MT/s, I only see 4.6 tok/s. *That
implies that my CPU is more than 10x less than what I need to saturate my
memory subsystem.* I'm using a recent build of llama-cli for this test, and a
relatively small context window (8k max).

In conclusion:

1. The theory behind token rate is very simple once you grok that LLMs are just
   autoregressors, and they need to page everything into memory once per token
   to operate.
2. You can extrapolate expected performance from smaller models, since memory
   bandwidth and compute dictate throughput in inverse proportion to model size.
3. People on the internet (especially redditors) are fucking stupid.

## meow meow meow meow
14 Apr 2025

meow meow meow meow meow meow meow meow. meow meow meow meow, meow meow
meow meow meow meow meow.

meow meow meow meow meow. meow meow meow meow meow meow meow, meow meow
meow. meow meow meow. meow meow meow meow meow meow meow meow meow. meow
meow meow; meow, meow meow meow meow meow meow meow.

meow meow meow meow meow. meow meow meow. meow meow.

## riding crop
7 Apr 2025

![Image of a 3D model of a riding crop.](./vr_assets/riding_crop/cover_photo.jpg)

[Click here](./vr_assets/riding_crop/riding_crop_v06.unitypackage) to download
my riding crop [from gumroad](https://yumfood.gumroad.com/l/riding_crop). See
the gumroad page for setup instructions.

Gumroad suspended my account over this product. Yes, over a fucking
*riding crop*. That's why it's hosted here. Enjoy the 100% discount <3

## a panoply of frameworks
3 Apr 2025

I want to use electron. I know that raw CSS sucks dick so let's use a
framework. Bootstrap sucks so let's use tailwind. Oh wait tailwind has a
build step? Okay let's use the CLI. Wait, I'm going to need to be able to
plumb runtime data eventually. I think that's what react is for right? Uhhh
if I'm using react is the tailwind CLI going to be good enough? It seems
like vite is what people are using for tailwind+react. Okay let's just
commit to that. Hmm this is a lot of setup, should I use a template? Oh
wait the main template people are using advertises "full access to node.js
apis from the renderer process." That seems like a terrible fucking idea.
Good thing I actually read the electron docs.

I am in pain.

## electron first impressions
1 Apr 2025

Occasionally I want to build some throwaway app for use by other people.
CLIs are nice and all, but they're hard to launch from VR, and most people
have never interacted with a terminal. So I need some way to write a
GUI. Enter electron.

Electron is a cross-platform UI framework. It bundles an entire chromium
install (gross) but in return you can basically just use standard web dev
practices.

It exposes a two-process model: one main process, and one renderer
process. The main process has basically unfettered access to the OS, and
the renderer process has unfettered access to the DOM (document object
model - the runtime structure of an HTML webpage). The two processes talk
to each other through channels.

Generating a distributable is easy with forge-cli. My main nitpick here
is that I think the default maker should be the zip maker, not the
installer. Installers give me the headache that I have to remember to
uninstall the thing once it most likely fails to work. Isolated
environments with no hidden side effects are simply better.
Switching to zip is simple matter of editing the default `forge.config.js`
and moving 'win32' to the maker-zip block. The generated .zip works
basically as expected: it contains a bunch of dependencies, and an .exe.
Put the .zip in a directory, extract it, double click the .exe, and you app
opens. (One more nit: the zip should contain a subdirectory so you can
extract without manually creating a directory for it.)
The hello world package is heavy but not as bad as I expected: 10.6MB
disk (compressed), 282MB disk (uncompressed), 0.0% CPU, 65MB memory. Memory
is basically in line with what I was getting with wxWidgets - I think that
was around 30 MB with my entire STT app built in. Worse but IMO within the
realm of reasonability. Time to first draw is pretty good - under a
second according to the eyeball test.

## hewwo wowld :3
20 Mar 2025

![me rn](./danser.gif)

