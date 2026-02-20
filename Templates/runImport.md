<%*
const file = tp.file.find_tfile(tp.file.title);

// 1. Preserve values you don’t want overwritten
const preserved = {
  project: tp.frontmatter?.project,
  hasTopic: tp.frontmatter?.hasTopic,
  aliases: tp.frontmatter?.aliases
};

// 2. Determine citekey + library
const citekey =
  tp.frontmatter?.citekey ??
  tp.frontmatter?.aliases?.find(a => a.startsWith('@')) ??
  tp.file.title;

const libraryID = tp.frontmatter?.libraryID ?? 1;

// 3. Run Zotero import
await app.plugins
  .getPlugin('obsidian-zotero-desktop-connector')
  .runImport('Literature Note', citekey, libraryID);

// 4. Give Obsidian a moment to finish writing
await new Promise(r => setTimeout(r, 600));

// 5. Restore preserved fields
await app.fileManager.processFrontMatter(file, fm => {
  if (preserved.project !== undefined) {
    fm.project = preserved.project;
  }
  if (preserved.hasTopic !== undefined) {
    fm.hasTopic = preserved.hasTopic;
  }
  if (preserved.aliases !== undefined) {
    fm.aliases = preserved.aliases;
  }
  
});
%>
