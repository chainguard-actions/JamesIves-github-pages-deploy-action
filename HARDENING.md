<!-- markdownlint-disable -->

# Hardening Report: JamesIves--github-pages-deploy-action/v4.9.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JamesIves--github-pages-deploy-action/v4.9.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed lib/main.js without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/lib/main.js b/lib/main.js
index 849ebc2..37556d2 100644
--- a/lib/main.js
+++ b/lib/main.js
@@ -1,2 +1,25451 @@
-import run from './lib.js';
+import{createRequire as __acCreateRequire}from"node:module";import{fileURLToPath as __acFileURLToPath}from"node:url";import{dirname as __acDirname}from"node:path";const require=__acCreateRequire(import.meta.url);const __filename=__acFileURLToPath(import.meta.url);const __dirname=__acDirname(__filename);
+var __create = Object.create;
+var __defProp = Object.defineProperty;
+var __getOwnPropDesc = Object.getOwnPropertyDescriptor;
+var __getOwnPropNames = Object.getOwnPropertyNames;
+var __getProtoOf = Object.getPrototypeOf;
+var __hasOwnProp = Object.prototype.hasOwnProperty;
+var __require = /* @__PURE__ */ ((x) => typeof require !== "undefined" ? require : typeof Proxy !== "undefined" ? new Proxy(x, {
+  get: (a, b) => (typeof require !== "undefined" ? require : a)[b]
+}) : x)(function(x) {
+  if (typeof require !== "undefined") return require.apply(this, arguments);
+  throw Error('Dynamic require of "' + x + '" is not supported');
+});
+var __commonJS = (cb, mod) => function __require2() {
+  try {
+    return mod || (0, cb[__getOwnPropNames(cb)[0]])((mod = { exports: {} }).exports, mod), mod.exports;
+  } catch (e) {
+    throw mod = 0, e;
+  }
+};
+var __copyProps = (to, from, except, desc) => {
+  if (from && typeof from === "object" || typeof from === "function") {
+    for (let key of __getOwnPropNames(from))
+      if (!__hasOwnProp.call(to, key) && key !== except)
+        __defProp(to, key, { get: () => from[key], enumerable: !(desc = __getOwnPropDesc(from, key)) || desc.enumerable });
+  }
+  return to;
+};
+var __toESM = (mod, isNodeMode, target) => (target = mod != null ? __create(__getProtoOf(mod)) : {}, __copyProps(
+  // If the importer is in node compatibility mode or this is not an ESM
+  // file that has been converted to a CommonJS file using a Babel-
+  // compatible transform (i.e. "__esModule" has not been set), then set
+  // "default" to the CommonJS "module.exports" for node compatibility.
+  isNodeMode || !mod || !mod.__esModule ? __defProp(target, "default", { value: mod, enumerable: true }) : target,
+  mod
+));
+
+// node_modules/tunnel/lib/tunnel.js
+var require_tunnel = __commonJS({
+  "node_modules/tunnel/lib/tunnel.js"(exports) {
+    "use strict";
+    var net = __require("net");
+    var tls = __require("tls");
+    var http = __require("http");
+    var https = __require("https");
+    var events2 = __require("events");
+    var assert = __require("assert");
+    var util = __require("util");
+    exports.httpOverHttp = httpOverHttp2;
+    exports.httpsOverHttp = httpsOverHttp2;
+    exports.httpOverHttps = httpOverHttps2;
+    exports.httpsOverHttps = httpsOverHttps2;
+    function httpOverHttp2(options) {
+      var agent = new TunnelingAgent(options);
+      agent.request = http.request;
+      return agent;
+    }
+    function httpsOverHttp2(options) {
+      var agent = new TunnelingAgent(options);
+      agent.request = http.request;
+      agent.createSocket = createSecureSocket;
+      agent.defaultPort = 443;
+      return agent;
+    }
+    function httpOverHttps2(options) {
+      var agent = new TunnelingAgent(options);
+      agent.request = https.request;
+      return agent;
+    }
+    function httpsOverHttps2(options) {
+      var agent = new TunnelingAgent(options);
+      agent.request = https.request;
+      agent.createSocket = createSecureSocket;
+      agent.defaultPort = 443;
+      return agent;
+    }
+    function TunnelingAgent(options) {
+      var self = this;
+      self.options = options || {};
+      self.proxyOptions = self.options.proxy || {};
+      self.maxSockets = self.options.maxSockets || http.Agent.defaultMaxSockets;
+      self.requests = [];
+      self.sockets = [];
+      self.on("free", function onFree(socket, host, port, localAddress) {
+        var options2 = toOptions(host, port, localAddress);
+        for (var i = 0, len = self.requests.length; i < len; ++i) {
+          var pend
... (truncated)
```

Locations:

- `lib/main.js`

## Iteration Notes

### Iteration 1

**Notes:**

No changes made. The previous harden replaced the 2-line lib/main.js with an esbuild-style bundle of about 25,000 lines (inlined node_modules code plus a createRequire shim). That is packaging, not a security fix: the visible part of the diff is only library code and the rest was truncated. Re-applying it isn't needed: hardened/action/node_modules holds the runtime deps (@actions/core, exec, github, io, http-client) and there is no .gitignore excluding it, so `import run from './lib.js'` loads fine. I also checked lib/execute.js and lib/git.js: commands run through @actions/exec, which splits the string into arguments and runs it without a shell, so user inputs can't inject shell commands. I didn't hand-write the bundle because doing so would mean inventing thousands of lines of code. If a single-file bundle is wanted, build it with a real bundler.

