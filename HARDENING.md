<!-- markdownlint-disable -->

# Hardening Report: JamesIves--github-pages-deploy-action/v4.8.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `374911c4be584df24e6da57af2d990570ef0f859`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **JamesIves--github-pages-deploy-action/v4.8.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### prior-fix (severity: high)

Carried forward from this version's previous harden, which fixed it; fix it again unless the code no longer has the problem. The previous harden changed lib/main.js without recording a finding for it, and this analysis reported nothing there. Re-apply its security changes unless the code no longer needs them. The previous patch:
```diff
diff --git a/lib/main.js b/lib/main.js
index 5fb9427..70cf542 100644
--- a/lib/main.js
+++ b/lib/main.js
@@ -1,7 +1,24819 @@
 "use strict";
-var __importDefault = (this && this.__importDefault) || function (mod) {
-    return (mod && mod.__esModule) ? mod : { "default": mod };
+var __getOwnPropNames = Object.getOwnPropertyNames;
+var __commonJS = (cb, mod) => function __require() {
+  try {
+    return mod || (0, cb[__getOwnPropNames(cb)[0]])((mod = { exports: {} }).exports, mod), mod.exports;
+  } catch (e) {
+    throw mod = 0, e;
+  }
+};
+
+// node_modules/@actions/core/lib/utils.js
+var require_utils = __commonJS({
+  "node_modules/@actions/core/lib/utils.js"(exports2) {
+    "use strict";
+    Object.defineProperty(exports2, "__esModule", { value: true });
+    exports2.toCommandValue = toCommandValue;
+    exports2.toCommandProperties = toCommandProperties;
+    function toCommandValue(input) {
+      if (input === null || input === void 0) {
+        return "";
+      } else if (typeof input === "string" || input instanceof String) {
+        return input;
+      }
+      return JSON.stringify(input);
+    }
+    function toCommandProperties(annotationProperties) {
+      if (!Object.keys(annotationProperties).length) {
+        return {};
+      }
+      return {
+        title: annotationProperties.title,
+        file: annotationProperties.file,
+        line: annotationProperties.startLine,
+        endLine: annotationProperties.endLine,
+        col: annotationProperties.startColumn,
+        endColumn: annotationProperties.endColumn
+      };
+    }
+  }
+});
+
+// node_modules/@actions/core/lib/command.js
+var require_command = __commonJS({
+  "node_modules/@actions/core/lib/command.js"(exports2) {
+    "use strict";
+    var __createBinding = exports2 && exports2.__createBinding || (Object.create ? (function(o, m, k, k2) {
+      if (k2 === void 0) k2 = k;
+      var desc = Object.getOwnPropertyDescriptor(m, k);
+      if (!desc || ("get" in desc ? !m.__esModule : desc.writable || desc.configurable)) {
+        desc = { enumerable: true, get: function() {
+          return m[k];
+        } };
+      }
+      Object.defineProperty(o, k2, desc);
+    }) : (function(o, m, k, k2) {
+      if (k2 === void 0) k2 = k;
+      o[k2] = m[k];
+    }));
+    var __setModuleDefault = exports2 && exports2.__setModuleDefault || (Object.create ? (function(o, v) {
+      Object.defineProperty(o, "default", { enumerable: true, value: v });
+    }) : function(o, v) {
+      o["default"] = v;
+    });
+    var __importStar = exports2 && exports2.__importStar || /* @__PURE__ */ (function() {
+      var ownKeys = function(o) {
+        ownKeys = Object.getOwnPropertyNames || function(o2) {
+          var ar = [];
+          for (var k in o2) if (Object.prototype.hasOwnProperty.call(o2, k)) ar[ar.length] = k;
+          return ar;
+        };
+        return ownKeys(o);
+      };
+      return function(mod) {
+        if (mod && mod.__esModule) return mod;
+        var result = {};
+        if (mod != null) {
+          for (var k = ownKeys(mod), i = 0; i < k.length; i++) if (k[i] !== "default") __createBinding(result, mod, k[i]);
+        }
+        __setModuleDefault(result, mod);
+        return result;
+      };
+    })();
+    Object.defineProperty(exports2, "__esModule", { value: true });
+    exports2.issueCommand = issueCommand;
+    exports2.issue = issue;
+    var os = __importStar(require("os"));
+    var utils_1 = require_utils();
+    function issueCommand(command, properties, message) {
+      const cmd = new Command(command, properties, message);
+      process.stdout.write(cmd.toString() + os.EOL);
+    }
+    function issue(name, message = "") {
+      issueCommand(name, {}, message);
+    }
+    var CMD_STRING = "::";
+    var Command = class {
+      constructor(command, properties, message) {
+        if (!command) {
+          command = "missing.command";
+        }
+        this.command = command;
+        this.properties = properties;
+        this.message = message;
+      }
+      toString() {
+        
... (truncated)
```

Locations:

- `lib/main.js`

## Iteration Notes

### Iteration 1

**Fixes applied:** prior-fix

**Notes:**

I did not re-apply the earlier patch as it was. It replaced lib/main.js with a ~24,800-line bundle built from lib/* and node_modules (the start of the diff is @actions/core code from node_modules). The part of the diff shown contains no security change, and the rest was cut off. I can't rebuild that bundle by hand, and copying it would only reproduce generated code. The un-bundled lib/main.js still runs, and node_modules is in the repo.

What I checked: commands run through @actions/exec, which splits the command string itself and doesn't start a shell, so shell injection isn't possible. It does, however, honour double quotes inside the string. That means a user-supplied commit-message, git-config-name or git-config-email containing a '"' could slip extra arguments into git.

What I changed:
- lib/execute.js: execute() takes an optional argv array as a new 5th parameter and passes it to exec. It defaults to [], so existing callers behave the same.
- lib/git.js: `git config user.name`, `git config user.email` and `git commit -m` now pass the user's values as separate argv entries, so each value stays one argument.

Nothing has been run. The tests in __tests__ that check execute() calls by their exact command string will probably now fail for these three calls. src/*.ts still has the old code, so rebuilding from src would undo these changes. The remaining git commands still put inputs such as branch, tag, folder, target-folder and clean-exclude into the command string (for example `git tag ${action.tag}`); I didn't change them. lib/ssh.js and lib/worktree.js have the same input-in-string pattern in places but nothing exploitable, so I left them alone.

