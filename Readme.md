To update from a GoogleDoc and save the output as html, use the `claat` tool.

eg. 
```
claat-linux-amd64 export 1fDRVc23lN8wi65TuxrLLhIGNG1huqCi7fHnv_9vD-oc
```

The above should **only** be done while actively editing the Google Doc. 

Once the document is ready for initial publication, it needs to be exported as markdown, again using the `claat` tool:

```
claat-linux-amd64 export -f md -o src 1fDRVc23lN8wi65TuxrLLhIGNG1huqCi7fHnv_9vD-oc
```

and the markdown and associated files can then be committed into this git repo. This is a **one time** step and from this point on ward, all changes to the codelab content need to be made in the **markdown** document and then the static html exported using the claat tool:

```
claat-linux-amd64 export src/your-first-flutter-app-to-appstore/index.md
```