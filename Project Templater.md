---
tags:
  - idea
  - dev-tool
  - project-specification
---
# Names
- Quilt
- Blanket
- Sew
- Knit
- Mesh
- Row

# Requirements First Round
- Should enable branched decisions with inheritance of state
- Reproducible between runs
- Revertable
- Enable deciding whether a file should have the following behaviors
	- Should continue to receive updates after manual changes applied?
	- Should stop receiving changes on conflicts?
	- Should never receive updates?
	- Should always overwrite with the latest?
- Distinct handling of configuration, cicd, code
- config specific
	- Recognize duplicate keys/sections despite location in file
	- Enable overwrite/update decisions on a key based level using glob syntax possibly
- Respect linting
- 

# Possible Requirements/Features
- Predefined rule sets
- Custom conditionals