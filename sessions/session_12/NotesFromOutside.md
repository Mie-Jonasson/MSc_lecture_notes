# **Notes from our outdoor lecture** on IaC and Terraform

Let me take notes on how the outdoor lecture worked. Most of you voted to go outside, so that's what we did. On the way there, I learned that one of your groups ended up paying  a lot of money to AWS. In the future we will warn students of this danger.

You already know what Infrastructure as Code is, so I only needed a quick introduction to the concept of Terraform state. Then I gave you four questions to discuss in groups while looking at the slides:

1. Benefits, pros and cons of the two approaches
2. Limitations of Terraform's declarativeness
3. How do we share state across the whole team?
4. What happens if the script or process crashes in the middle?

You worked in groups for 20 minutes. 20 minutes was a bit short almost nobody got to the fourth question. But you all worked nicely and we had a good discussion after that. 

The summary of the discussions is below. 

### Benefits and Limitations

**On the benefits**, you came up with a lot of observations: **readability**, **conciseness, convenience of state tracking**. You also raised some nice limitations: 
- what if Terraform doesn't support your specific infrastructure? 
- One of you mentioned that you might want a specific low-level functionality that isn't captured in the abstraction Terraform offers. 
- Another good observation: Terraform is no longer open source. It's now OpenTofu (the open source fork) so one has to move or pay. In general, since using Terraform adds an extra dependency to your stack, all the pros and cons of taking on a new dependency apply. 

You were really good at discussing this part. It was really nice.


It was a good moment to introduce Joel Spolsky's concept of [Leaky Abstractions](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/). I recommend it as reading. It really is a reality: your abstraction will always leak, no matter what it is: ORMs, Mobile App Abstraction Layers, Infrastructure Abstractions, etc.

Related to this, I also shared a principle I try to follow: **do it in the simplest way you can, unless it becomes so complicated that learning a standard solution is actually easier.** Terraform is complicated, but it's probably better than 10,000 lines of undocumented shell scripts written by Mircea. 

The inflection point is when your homegrown simple solution has grown into something that costs more to maintain than learning the standard tool would. Until you hit that point, prefer simple. 

Also, we discussed the case study of that company who was dependent on a single developer, because only he knew what the hell was in the codebase. 

### Limits of Terraform Declarativeness 
**On the second question (limitations of declarativeness)**, only one of you spotted the answer I was looking for: the "*stringly typed*" provisioners that execute bash commands on the target machine are falling outside of the state tracking of Terraform. 

We then discussed the limitations of Terraform's abstraction model more broadly: it works with cloud resources, but not with the software running inside the machine. 

I mentioned that there are equivalent declarative specifiers for the software inside the machine: namely Ansible, Puppet, and similar provisioning tools. One of you brought up NixOS as the ultimate holy grail of declarative specification of everything on your OS. *(On the way back to the lecture room I discussed with some of you about how this sounds super nice in theory, but it can be very tedious in practice, and it really does not pay off if you have to reinstall a machine once every two years).*

### How To Share State with the Team

**On the third question (sharing state)**, there was a lot of discussion. Many of you insisted that it would be good to use GitHub, especially if the repo is private. I had to push back and explain the race condition problem, and also the fact that there could be secrets in the state file. HashiCorp's own docs warn that state files can contain database passwords, API tokens, and similar sensitive values ([reference](https://developer.hashicorp.com/terraform/language/manage-sensitive-data)).

The real solution is a locking mechanism around the shared state ([HashiCorp's official docs on state locking](https://developer.hashicorp.com/terraform/language/state/locking)). Before you apply, you acquire a lock; you hold it for the duration of the apply; then you release it. While you hold the lock, no one else can start an apply. This is what prevents two people from simultaneously mutating the same infrastructure. The lock typically lives in a database that supports atomic operations: you can use a managed service like Terraform Cloud that bundles it all together. Or you could roll your own with a PostgreSQL transaction. Any system that gives you *atomic compare-and-swap* will work.

All of this locking machinery can be overkill for many real situations, though. One of you mentioned that your team has something similar for your own software stack provisioning and (some kind of Ansible-like specs?) you don't use any read-write-transactional system but just a GitHub private repo. We agreed that if provisioning only happens once a month, the probability of a race condition is practically nonexistent.


### Resume After Crash

**On the fourth question (crashes mid-process)**, one of you wondered what happens if Terraform crashes _after_ it has sent a request to a cloud provider to boot up a machine. Would it restart and always crash at the same point? 

I said that it depends on the actual Terraform implementation. I looked it up, and this is what happens: when you run `terraform apply`, what's actually happening is: 
> 
> 1. Acquire a lock on the state file (via DynamoDB or whatever the backend uses)
> 2. Read the current state
> 3. Mutate the real world (cloud resources) according to the plan
> 4. Write the updated state back to storage
> 5. Release the lock

Obviously, if the process crashes after step 3, then some resources will be "drifting" -- the state file does not know about them. Next time you run terraform plan, Terraform compares the state file against reality by querying the cloud provider, and shows you the drift. The following apply will then either adopt the resource, or destroy and recreate it, depending on whether it matches what your configuration declares. (HashiCorp has a [tutorial on drift detection](https://developer.hashicorp.com/terraform/tutorials/state/resource-drift) if you want to see this in action.)

The other weak point, and we've talked about it already, are the provisioner blocks inside a resource: if `remote-exec` fails halfway through, Terraform marks the resource as "tainted" and destroys/recreates it on the next apply, rather than trying to resume the provisioning. Which brings us back to the leaky abstraction discussion.

But overall, crashing is still handled better by Terraform than by a Bash script — unless you work hard to make the Bash script idempotent, in which case you should probably just switch to Terraform anyway.

### Read More

- If you want to go deeper, [the full slide deck](./IaC.pdf) contains material we didn't cover outdoors — planning, modules, IAM, and more on state management. Read it with the same comparative lens we used today: what problem is this solving? What would the Bash equivalent look like? What's the tradeoff?

- Joel Spolsky's concept of [Leaky Abstractions](https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/)

### Documentation 

We also talked about [how to document your architecture](./Documentation). 
