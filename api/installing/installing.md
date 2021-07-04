# Installing Yukitoki

## Steps
- `git clone https://github.com/DevSnowflake/Yukitoki`
- `npm install`

## Config
- Edit `config.js`
- Go to `src/data` and add your source, example `MainSource.js`:
  ```js
    import semver from "semver";
    import DocsManager from "./DocsManager";

    const blacklisted = new Set(["old", "gh-pages", "docs"]); // branches to not include in docs

    export default new DocsManager({
        id: "main", // required: /docs/main
        name: "Main", // source name
        global: "DiscordPlayer", // will append DiscordPlayer. before constructor name
        repo: "Androz2091/discord-player", // the github repo where your docs are located at
        defaultTag: "master", // /docs/main/master
        docsBranch: "docs", // name of the branch where your docs files are located at
        defaultFile: { // the file shown after loading: /docs/main/master/general/welcome
            id: "welcome",
            category: "general"
        },
        branchFilter: (branch) => !blacklisted.has(branch) && !branch.startsWith("dependabot/"), // if you wanna filter branches
        tagFilter: (tag) => semver.gte(tag.replace(/^v/, ""), "4.1.4") // if you wanna filter tags
    });
  ```

- Go to `/src/data/sources.js` and add source:
  ```js
    import MainSource from "./MainSource";
    import OtherSource from "./OtherSource";

    const sources = {
        [MainSource.id]: MainSource,
        [OtherSource.id]: OtherSource
    };

    export default sources;
  ```

- Go to `/src/pages/docs.svelte` and add the default source
  ```html
    <script>
        import Loader from "~/components/Loader.svelte";
        import Navbar from "~/components/Navbar.svelte";
        import Footer from "~/components/Footer.svelte";
        import MainSource from "~/data/MainSource";
        import DocsStore from "~/app/DocsStore";
        import { goto } from "@roxi/routify";
        const docsSource = new DocsStore(MainSource);

        docsSource.fetchDocs().then(() => {
            $goto(`/docs/${docsSource.manager.id}/${docsSource.manager.defaultTag}/${docsSource.manager.defaultFile.category}/${docsSource.manager.defaultFile.id}`);
        });
    </script>
    <Navbar />
    <Loader />
    <Footer />
  ```

- Preview: `npm run dev`

# Deploying
You can use free services such as **[Netlify](https://netlify.com)** to deploy your docs.
