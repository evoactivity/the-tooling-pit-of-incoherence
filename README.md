# The tooling pit of incoherence

| Why multiple language servers? | Which package for syntax highlighting? | 
|--------------------------------|----------------------------------------|
|  <img width="471" alt="image" src="https://github.com/evoactivity/the-tooling-pit-of-incoherence/assets/83799/fe2aa005-ceb4-446a-9b69-e7562b8a55ef"> | <img width="475" alt="image" src="https://github.com/evoactivity/the-tooling-pit-of-incoherence/assets/83799/d1e63ddc-f3c9-4c19-9d35-7596175cc70c"> |

I've spent the last week working on making the `.gjs, .gts` experience in VS Code closer to the standard `.js, .ts, .hbs` experience. If ember is currently in the "pit of incoherence", then the tooling should be climbing out of that pit and clearing the way for polaris to land safely. We should be able to experiment today with what the pit is promising, without our tools breaking down on us. This is something I think the community can help with without needing a huge amount of ember specific knowledge.

I think there are three goals that need to be achieved before polaris can safely be recommended.

1) One official language server
2) One official syntax definition
3) Unifying this tooling under the Ember Tooling organisation

Much of the tooling work has already been started with @dfreeman working on glint and @lifeart continuing the work on Ember Language Server and creating the Glimmer Template Syntax VS Code extension.

So a lot of the work is already done or started! For the parts we do not have complete, we have good examples for inspiration with [Vue](https://github.com/vuejs/language-tools) and [Svelte](https://github.com/sveltejs/language-tools) both offering `language-tools` packages doing many of the same things we need to do.

As I've been going over all this the last week I've been brain dumping into this document and I thought I'd make it public and see what can be organised and if people agree with my thinking on this.

---

## Ember Tooling GitHub Organisation

This should be the only place where our tooling lives. Efforts should be made to bring the work already done back into this org and some general housekeeping.

- Can we upstream lifeart's language server and syntax package back into ember-tooling?
	- @lifeart would you be onboard with that?
- Can we add neovim configurations/addons
	- I think @nullvoxpopuli has a working configuration?
- Does anyone use `ember-cli-remote-inspector`?
	- Does it still work? Not been maintained for 9 years
	- Can we archive it?
- Is the `ember-tooling/prettier-vscode` repo still needed?
	- if not can we archive or delete?
- We should spruce up the ember-tooling organisation. Add some logos and nicely formatted readme's.
- Can we verify the vscode account like vue has, so the ember tooling extensions get a blue badge?
- VS Code extensions should be consistently named eg
	- `lifeart.vscode-ember-unstable` -> `emberjs.vscode-ember-language-server`
	- `lifeart.vscode-glimmer-syntax` -> `emberjs.vscode-ember-syntax-highlighting`
- Publish vscode extensions to [Open VSX Registry](https://open-vsx.org/)
	- Allows vs code derived editors to installed extensions like vs codium; as they are not allowed to use the official vs code marketplace
- Need to write contributing guides covering
	- How to work on and test a vs code extensions
	- How to work on and test neovim configurations
- Investigate using [changesets](https://github.com/changesets/changesets) to automate releases
	- Lifeart's workflow works, but I believe could be simplified and improved through changesets 
	- Open Questions
		- How to disable npm publish
		- How to intercept release workflow to publish to code marketplaces instead
- A `language-tools` monorepo under `emberjs` org would be in-line with other frameworks, but it makes sense to get everything under the tooling org first since it already exists.

---

## Bespoke Language Servers

These are going to become even more important in a polaris world. The new `.gts` and `.gjs` files are not understood by typescript, so our tools are going to need to rise to meet expectations set by typescript.

- Two language servers currently, Glint and Ember Language Server.
	- They complement each other rather than compete
- Are there overlapping goals with glint-language-server and ember-language-server? Should efforts be combined somewhat?
	- @dfreeman, @lifeart do you have any thinking around this?
- Would it make sense to have glint be provided to vscode exclusively through the ELS extension instead of needing two vscode extensions to be maintained/communicated/installed?
	- I see @lifeart has a spike repo using glint as an addon in ELS, did you find that to be viable?
- Glint should probably come under ownership of the ember-tooling org too.
- All other language server extensions should be deprecated if not deleted entirely. Any unstable features can be published as a pre-release and users can opt in by installing the pre-release version.

---

## chiragpat.vscode-glimmer

This is a fairly popular extension, so I would recommend merging my PR's so current users get improved syntax highlighting and then deprecating the extension in favour of `lifeart.vsc-glimmer-syntax` so there is only one extension that effort can be focused on.

[Ember guides](https://guides.emberjs.com/release/code-editors/#toc_syntax-highlighting) and [ember-template-imports](https://github.com/ember-template-imports/ember-template-imports) currently recommend this plugin, we should raise PR's to only recommend `lifeart.vsc-glimmer-syntax`.

---

## Things broken when using gts/gjs today

These are things we should fix so no one is losing a feature that currently works when moving to `gts` and `gjs`. Please add issues you have come across.

- ~~vscode: Syntax highlighting is broken or sub-par for template tags~~
- ~~vscode: Code folding is only offered for template tags~~
	- Fix here is only partially enabled though default indentation detection being enabled by `lifeart.vsc-glimmer-syntax`
- vscode: Auto closing html tags does not work inside template tags (does work in `.hbs` files)
- vscode: A `glimmer-js` file inside a typescript project syntactically is treated as typescript (types are not syntax errors as they would be in a js file) and glint does not run properly, but it successfully builds
	- I do see `.gjs` as an extension in glint extension source, so I'm not sure why it's not working. @dfreeman any ideas?
- vscode: Multi-root workspaces can cause glint to duplicate hover information
	- https://github.com/typed-ember/glint/issues/602
- Semantic tokens are not provided by either language server so additional syntax highlighting (decorators etc) is not possible

---

## Work on the table

- Writing contributing guides
- Creating a [changesets](https://github.com/changesets/changesets) based workflow to release vs code extensions
- Review and rewrite [Ember Guides / Ember Extensions](https://guides.emberjs.com/release/code-editors) page
- Upstreaming lifearts work back into Ember Tooling org
- Ember Language Server should provide
	- Semantic Scope Tokens for additional syntax highlighting
	- Code Folding for JS/TS parts of `.gjs/.gts` files
		- https://github.com/lifeart/vscode-ember/issues/43
- Third party code we could PR
	- VS Code extension [remove unused imports](https://marketplace.visualstudio.com/items?itemName=kuscamara.remove-unused-imports) doesn't work with gjs/gts, it uses typescript under the hood to parse and the template tags breaks it
	- prettier plugin [@trivago/prettier-plugin-sort-imports](https://github.com/trivago/prettier-plugin-sort-imports) will need a template tag compiler

---

## How you can help

You're inside your editor most of the day right? Try things like `Glint` and `ember-template-imports`, let us know how well it works for you (in terms of editor experience). Are you missing some key functionality you rely on in normal `.js, .ts, .hbs` contexts? Is something just behaving strangely? Are you not seeing the correct syntax highlighting?

1) Report issues you run into to the relevant [Ember Tooling](https://github.com/ember-tooling) repository
	- Until unified under Ember Tooling, it'll probably be one of these repos
		- [Glimmer Template Syntax for VS Code](https://github.com/lifeart/vsc-ember-syntax)
		- [Stable Ember Language Server](https://github.com/lifeart/ember-language-server)
		- [VS Code Language Server Client](https://github.com/lifeart/vscode-ember/)
		- [Glint](https://github.com/typed-ember/glint/)
2) If you feel up for a challenge clone the relevant repo and start hacking at a solution to your issue. To begin with we are aiming for working not perfect :)
	- Useful resources 
		- https://code.visualstudio.com/api
		- https://code.visualstudio.com/api/language-extensions/overview
		- https://github.com/microsoft/vscode/tree/main/extensions/typescript-basics
		- https://github.com/microsoft/vscode/tree/main/extensions/typescript-language-features
3) Take ownership of another editor we are not focusing on and work on your own extensions integrating the language servers and syntax highlighting. Actively maintained extensions could be promoted to the `ember-tooling` org.
