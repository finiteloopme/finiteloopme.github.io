---
layout: post
title: "Policy Management in Cloud Native Environment"
published: true
categories:
    - technical
tags:
    - kubernetes
    - openshift
    - programming
    - opa
    - policy
---

# Policy Management in Cloud Native Environments

## Overview

In very simplistic terms, there are three (3) things involved in policy mananagement.
1. _Policy_ is a set of rules that governs the behaviour of the service.
2. _Data_ is the available information to evaluate the policy against.  
3. _Service_ is seeking clarification if we should comply with the request given a set of _data_.

Policies include important knowledge about how to deal with legal requirements, adhere to technical constraints, ensure compliance with security regulations, enfore decision in a consistent manner, etc.

## Policy Enablement

Traditionally policies have been applied manually based on documented rules or unspoken conventions.  Policies may also be enforced in software using application logic, which may be configured at deployment time and in a few cases at runtime.

[Open Policy Agent][1] project highlights the notion of _[Policy Enablment][2]_.
>Policy-enabled services allow policies to be specified declaratively, updated at any time without recompiling or redeploying, and enforced automatically (which is especially valuable when decisions need to be made faster than humanly possible). 

### Why Policy Enablement

Three key advantages offered by _policy enablement_ are:
1. Decoupling of _policy evaluation_ from the service seeking to comply with that polciy allows for the lifecylce of the policy to be managed independently of the service.
2. Ability to declaratively create and apply the policies. As an example use of _declarative logic programming language_ like [Rego][3] empowers users to read, write, and manage these rules without needing specialized development or operational expertise.
3. _Queries_ (policies) under consideration and the correspnding _data set_ can be easily input to the policy engine at runtime.  This allows promotes _automated testing_ of the policies.

## An Example of context being used in policy mangement

An _organisation_ has grouped their employees as below.  
- Admin
   1. Alice
   2. Bob
   3. Clarke
   4. Doug
- Dev
   1. Ethan
   2. Felicity
   3. Gina
   4. Harry
- Ops
   1. Ivan
   2. Jasper
   3. Katie
   4. Lina

Now this organisation primarily allows their _Ops_ team to perform deployments.  This would be a normal _RBAC_ style of role based policy.

As a secondary requirement, they also want to ensure that deployment should be allowed only if certain technical criteria is met.  Like there is enough capacity on the cloud (internal or external), time of the day, there are minimum of two developers scheduled to be on call, etc.
Let us assume that minimum of two developers on call is the only secondary requirement.

> As a pre-requisite, ensure you have `opa` installed[^1]
 
```json
cat >users.json <<EOF
{
    "admin": [
        {"name": "Alice"},
        {"name": "Bob"},
        {"name": "Clarke"},
        {"name": "Doug"}
    ],
    "dev": [
        {"name": "Ethan"},
        {"name": "Felicity"},
        {"name": "Gina"},
        {"name": "Harry"}
    ],
    "ops": [
        {"name": "Ivan"},
        {"name": "Jasper"},
        {"name": "Katie"},
        {"name": "Lina"}
    ],
    "on_call":[
        {"name": "Gina", "group": "dev"},
        {"name": "Felicity", "group": "dev"},
        {"name": "Jasper", "group": "ops"},
        {"name": "Bob", "group": "admin"}
    ]

}
EOF
```

```s
cat >deploy.rego <<EOF
package opa.deploy

import data.admin
import data.dev
import data.ops
import data.on_call

# Input param:
#   string group: group name of people required on call
#   int required_count: minimum people required on call from that particular group
# Output deploy
#   boolean deploy: true if the criteria is met, else false
can_deploy(group, required_count) = deploy {
    # Criteria
    #   1. Get all the names from on_call given a group name
    #   2. Ensure that the on_call user exists in original list
    names = [ name | on_call[i].group = group; on_call[i].name = name; dev[_].name = name ]
    #   3. Count the number of names in the list
    count(names, _required_count)
    #   4. Check if req count is greater than or equal to specified count
    deploy = _required_count>=required_count
}


EOF
```

```bash
# Should return `c = true`
data.opa.deploy.can_deploy("dev", 2, c)

# Should return `c = false`
data.opa.deploy.can_deploy("dev", 7, c)
```

[1]: https://www.openpolicyagent.org/
[2]: https://www.openpolicyagent.org/docs/#what-is-policy-enablement
[3]: https://www.openpolicyagent.org/docs/how-do-i-write-policies.html

---

[^1]: https://www.openpolicyagent.org/docs/get-started.html#prerequisites
