# License Setup Guide for Autogent MCP Repositories

This guide helps you set up the MIT License correctly across all Autogent MCP repositories.

## 📄 License Choice: MIT

We've chosen the **MIT License** for the Autogent MCP ecosystem because it:

- **Maximizes Adoption**: Allows commercial use without restrictions
- **Simplifies Compliance**: Easy to understand and comply with
- **Encourages Contributions**: Low barrier for contributors
- **Enterprise Friendly**: No copyleft restrictions

## 🔧 Setup Instructions

### 1. Add LICENSE File to Each Repository

Create a `LICENSE` file in the root of each repository:

```
MIT License

Copyright (c) 2025 Autogent MCP

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

### 2. Update README Files

Add license badge and section to each README:

#### License Badge
```markdown
![License](https://img.shields.io/badge/license-MIT-blue.svg)
```

#### License Section
```markdown
## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

### What this means for you:
- ✅ **Commercial Use**: Use in commercial applications
- ✅ **Modification**: Modify source code freely  
- ✅ **Distribution**: Distribute and sublicense
- ✅ **Private Use**: Use for private projects

### Requirements:
- Include copyright notice and license text in any copies
- Preserve license and copyright notices

For more details, see the [Contributing Guide](https://autogentmcp.github.io/contributing/#license).
```

### 3. Add License Headers to Source Files

#### Java Files
```java
/*
 * Copyright (c) 2025 Autogent MCP
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in all
 * copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 * SOFTWARE.
 */
```

#### Python Files
```python
# Copyright (c) 2025 Autogent MCP
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in all
# copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.
```

#### TypeScript/JavaScript Files
```typescript
/*
 * Copyright (c) 2025 Autogent MCP
 *
 * Permission is hereby granted, free of charge, to any person obtaining a copy
 * of this software and associated documentation files (the "Software"), to deal
 * in the Software without restriction, including without limitation the rights
 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
 * copies of the Software, and to permit persons to whom the Software is
 * furnished to do so, subject to the following conditions:
 *
 * The above copyright notice and this permission notice shall be included in all
 * copies or substantial portions of the Software.
 *
 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
 * SOFTWARE.
 */
```

### 4. Package Configuration

#### Java (pom.xml)
```xml
<licenses>
    <license>
        <name>MIT License</name>
        <url>https://opensource.org/licenses/MIT</url>
        <distribution>repo</distribution>
    </license>
</licenses>
```

#### Python (setup.py/pyproject.toml)
```toml
[project]
license = {text = "MIT"}
```

#### Node.js (package.json)
```json
{
  "license": "MIT"
}
```

### 5. Repository Settings

#### GitHub Repository Settings
1. Go to repository Settings → General
2. Under "Features" → "Issues", check "Set up templates"
3. Add license information to issue templates
4. Update repository description to include license

#### GitHub License Detection
GitHub should automatically detect the MIT license from your LICENSE file.

## 🔍 Repository Checklist

For each repository, ensure:

- [ ] `LICENSE` file in root directory
- [ ] License badge in README
- [ ] License section in README
- [ ] License headers in source files (optional but recommended)
- [ ] Package configuration includes license
- [ ] GitHub detects license correctly

## 📋 Repository Status

| Repository | License File | README Badge | Package Config | Status |
|------------|--------------|--------------|----------------|--------|
| [Portal](https://github.com/autogentmcp/portal) | ❌ | ❌ | ❌ | To Do |
| [Registry](https://github.com/autogentmcp/mcp-registry) | ❌ | ❌ | ❌ | To Do |
| [Autogent Server](https://github.com/autogentmcp/autogentmcp_server) | ❌ | ❌ | ❌ | To Do |
| [Java SDK](https://github.com/autogentmcp/mcp-core-java) | ❌ | ❌ | ❌ | To Do |
| [Python SDK](https://github.com/autogentmcp/mcp-core-python) | ❌ | ❌ | ❌ | To Do |
| [Node.js SDK](https://github.com/autogentmcp/mcp-core-node) | ❌ | ❌ | ❌ | To Do |
| [Demo Apps](https://github.com/autogentmcp/mcp-demo-apps) | ❌ | ❌ | ❌ | To Do |
| [Documentation](https://github.com/autogentmcp/autogentmcp.github.io) | ✅ | ✅ | ❌ | In Progress |

**Legend**: ✅ = Complete, ❌ = To Do, 🔄 = In Progress

## 🚀 Next Steps

1. **Apply to all repositories**: Add LICENSE files to all repositories
2. **Update documentation**: Add license information to all README files
3. **Configure packages**: Update package configurations
4. **Automation**: Consider GitHub Actions for license checking
5. **Legal review**: Consider legal review for enterprise use

## 📞 Questions?

If you have questions about licensing:
- Review the [Contributing Guide](https://autogentmcp.github.io/contributing/#license)
- Check [Choose a License](https://choosealicense.com/licenses/mit/) for MIT details
- Consult legal counsel for complex licensing questions

---

*This guide ensures consistent licensing across the Autogent MCP ecosystem.*
