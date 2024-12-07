# Hello world

!!! abstract "Fix a github issue with SWE-agent"
    In this tutorial, we will fix a github issue with SWE-agent using the command line interface.

    * Make sure you have [installed](../installation/index.md) SWE-agent and have a [language model](../installation/keys.md) set up.
    * We will be executing code in a Docker sandbox, so make sure you have docker installed ([docker troubleshooting](../installation/tips.md)).
      If you cannot run docker, skim this tutorial and see how you can run SWE-agent with cloud-based execution in the [command line basics tutorial](cl_tutorial.md).

After installing SWE-agent, you have the `sweagent` command available. Run `sweagent --help` to see the list of subcommands.
The most important ones are

* `sweagent run`: Run SWE-agent on a single problem statement.
* `sweagent run-batch`: Run SWE-agent on a list of problem statements. This is what you would use for benchmarking, or when
  working with a larger set of historic issues.

In this tutorial, we will focus on the `run` subcommand.

Let's start with an absolutely trivial example and solve an issue about a simple syntax error ([`swe-agent/test-repo #1`](https://github.com/SWE-agent/test-repo/issues/1))

```bash
python run.py \
  --agent.model.name=gpt4 \
  --agent.model.per_instance_cost_limit=2.00 \  # (1)!
  --env.repo.github_url=https://github.com/SWE-agent/test-repo \
  --problem_statement.github_url=https://github.com/SWE-agent/test-repo/issues/1
```

1. This limits the inference cost per instance to $2. The default is $3.

!!! tip "Annotations"
    Notice the :material-chevron-right-circle: icon in the right margin in the code snippet? Click on it to display more information
    about the line.

The example above uses the `gpt4` model from OpenAI. In order to use it, you need to add your OpenAI API key to the environment:

```bash
export OPENAI_API_KEY=<your key>
```

alternatively, you can create a `.env` file in your working directory and put the key in there.
More information about environment: TODO


TODO: Update output

<details>
<summary>Output</summary>

```json
--8<-- "docs/usage/cl_tutorial_cmd_1_output.log"
```
</details>

As you can see, the command line options are hierarchical. At the top level, there are three important sections:

* `problem_statement`: What problem are you trying to solve?
* `agent`: How do you want to solve the problem? This includes setting up the LM with `--agent.model`.
* `env`: What is the environment in which the problem statement should be solved?
  This includes setting the repository/folder with the source files with `--env.repo`, as well as docker images and other dependencies.
  This will also control where the code is executed (in a local container or in the cloud).


Watching the output, you can notice several stages:

1. Setting up the **deployment**: SWE-agent lets LMs execute actions in sandboxed environments. It can run these environments
   in docker containers (default), on modal, AWS fargate, or directly on your computer (not recommended).
   When the deployment starts, you will notice a "starting runtime" message that takes a few seconds. The runtime is
   what is executing the commands within your deployment.
2. Setting up **tools**: The tools that you specified are copied and installed within the environment.
3. **System and instance prompts**: The initial instructions are shown to the LM.
4. **Main loop**: The LM starts to suggest and execute actions.
5. **Submission**: The LM calls `submit` and we extract the patch (i.e., the changes to the source code that solve the problem).

The complete details of the run are saved as a "trajectory" file (more about them [here](trajectories.md)). They can also be turned into new [demonstrations](../config/demonstrations.md) together with other log and output files.

Wetted your appetite? Head over to the [command line basics tutorial](cl_tutorial.md) to learn more about the options.