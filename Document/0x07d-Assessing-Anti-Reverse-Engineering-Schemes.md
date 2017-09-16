# Assessing Software Protection Schemes

Software protections are a controversial topic. Some security experts dismiss client-side protections outright. Security-by-obscurity, they argue, is not *real* security, thus from a security standpoint no value is added. In the MASVS and MSTG we take a more pragmatic approach. Given that software protection controls are used fairly widely in the mobile world, we argue that there is *some* benefit to such controls, as long as they are employed with a clear purpose and realistic expectations in mind, and aren't used to *replace* solid security controls.

Mobile app security testers encounter anti-reversing defenses in their daily work, and they not only need ways to "deal" with them to enable dynamic analysis, but also to assess whether these mechanisms are used appropriately and effectively. Advice like "you must use obfuscation" or "never obfuscate code because it's useless" isn't useful to clients. You should be able to provide a measured assessment whether anti-reversing measures should be used in a particular case, and test the effectiveness of a given set of defenses.

The point of software-based reversing defenses is to add *obscurity* - enough to deter a group of adversaries from achieving certain goals. There are several reason why one would opt to do this: For example, to make it more difficult to steal parts of the source code and IP, or to increase the effort needed to for cheating in an online game.

Resilience testing is the process of evaluating the robustness of the a software protection scheme against particular threats. Typically, this kind of testing is performed using a black-box approach, with the objective of circumventing the software protection scheme and reaching a pre-defined goal, such as extracting sensitive assets. This process requires skills that are not typically associated with penetration testing: The tester must be able to handle advanced anti-reversing tricks and obfuscation techniques. Traditionally, this is the domain of malware analysts.

In prior chapters, we have already seen many anti-reversing tricks employed in mobile apps: Anti-debugging, root detection, integrity checks, and others. We've also introduced reverse engineering techniques, many of which would allow one to deactivate those defenses. In this chapter, we provide the high-level framework needed for a more systematic assessment.

The effectiveness of software protection schemes depends to some extent on originality and secrecy. Standardizing a particular protection scheme has the side effect of making the scheme ineffective: Soon enough, someone will release a generic tool for bypassing the scheme.

It is impossible to provide a step-by-step guide for cracking every possible protection scheme. A resilience assessment can be performed only by a skilled reverse engineer who is familiar with the state-of-the-art in mobile app reversing and anti-reversing. In the following section, we provide the following:

1. List a number of reverse engineering processes against that should be defended against;
2. Highlight key properties that determine the effectiveness of the techniques used;
3. List robustness criteria for specific types of obfuscation and anti-tampering;
3. Provide testers with knowledge, processes and tools for verifying effectiveness.

## Resilience Assessment Overview

A *resilience assessment* can be performed in the context of a regular mobile app security test, or stand-alone to verify the effectiveness of a software protection scheme. The process consists of the following high-level steps:

1. Assess whether a suitable and reasonable threat model exists, and the anti-reversing controls fit the threat model;
2. Assess the effectiveness of the defenses in countering the identified threats using hybrid static/dynamic analysis. In other words, play the role of the adversary, and crack the defenses!
3. In some scenarios, white-box testing can be added to assess specific features of the protection scheme in an isolated fashion (e.g., a particular obfuscation method).

## Assessing the Threat Model and Software Protection Architecture

Client-side protections can be unnecessary or even counter-productive in some cases. In the worst case, software protections cause a false sense of security and encourage bad programming practices. For this reason, proper attack modeling is a necessary prerequisite before implementing any form of software protections. The threat model must clearly outline the client-side threats defended against. 

Note that the threat model must be sensical. For example, hiding a cryptographic key in a white-box implementation is besides the point if the attacker can easily code-lift the white-box as a whole. Also, the expectations as to the effectiveness of scheme must be specified.

The [OWASP Reverse Engineering and Code Modification Prevention Project](https://www.owasp.org/index.php/OWASP_Reverse_Engineering_and_Code_Modification_Prevention_Project) lists the following technical threats associated with reverse engineering and tampering:

- Tampering - Attackers may wish to alter higher-level business logic embedded within the application to gain some additional value for free. For instance, an attacker may alter digital rights management code embedded in a mobile application to attain digital assets like music for free;

- Repudiation - Attackers may disable logging or auditing controls embedded within the mobile application to prevent an organization from verifying that the user performed particular transactions;

- Information Disclosure - Attackers may seek to extract valuable assets from the mobile app, such as a proprietary algorithm;

## The Assessment Process

Software protection effectiveness can be assessed using the white-box or black-box approach. Just like in a "regular" security assessment, the tester performs static and dynamic analysis but with a different objective: Instead of identifying security flaws, the goal is to identify holes in the anti-reversing defenses, and the property assessed is *resilience* as opposed to *security*. Also, scope and depth of the assessment must be tailored to specific scenario(s), such as tampering with a particular function.

<img src="Images/Chapters/0x07b/blackbox-resiliency-testing.png" width="650px" />

### Black-box Resilience Testing

In a nutshell, this is almost the same as solving a CTF challenge - the only difference is that a systematic assessment of difficulty is performed as well.

The advantage of the black-box approach is that it reflects the real-world effectiveness of the reverse engineering protections: The effort required by actual adversary with a comparable skill level and toolset would likely be close to the effort invested by the assessor. 

Drawbacks: For one, the result is highly influenced by the skill level of the assessor. Also, the effort for fully reverse engineering a program with state-of-the-art protections is very high (which is exactly the point of having them), and some apps may occupy even experienced reverse engineers for weeks. Experienced reverse engineers aren’t cheap either, and delaying the release of an app may not be feasible in an "agile" world. 

### White-box Resilience Testing

-- [ TODO ] -- 

## Key Questions to Answer

Any resilience test should answer the following questions:

**Does the protection scheme impede the threat(s) they are supposed to?**

It is worth re-iterating that there is no anti-reversing silver bullet. 

**Does the protection scheme achieve the desired level of resilience?**

It is worth re-iterating that there is no anti-reversing silver bullet. 

**Does the scheme defend comprehensively against processes and tools used by reverse engineers?**

--[ TODO ] --

**Are suitable types of obfuscation used in the appropriate places and with the right parameters?**

--[ TODO ] --

## What To Defend Against

--[ TODO ] --

<img src="Images/Chapters/0x07b/reversing-processes.png" width="600px" />

## What Makes Anti-Reversing Tricks Effective

The main motto in anti-reversing is **the sum is greater than its parts.** The defender wants to make it as difficult as possible to get a first foothold for an analysis. They want the adversary to throw the towel before they even get started! Because once the adversary does get started, it's usually only a matter of time before the house of card collapses.

To achieve this deterrent effect, one needs to combine a multitude of defenses, preferably including some original ones. The defenses need to be scattered throughout the app, but also work together in unison to create a greater whole. In the following sections, we'll describe the main criteria that contribute to the effectiveness of programmatic defenses.

#### Amount and Diversity

--[ TODO ] --

As a general rule of thumb, at least two to three defensive controls should be implemented for each category. These controls should operate independently of each other, i.e. each control should be based on a different technique, operate on a different API Layer, and be located at a different location in the program (see also the criteria below). The adversary should not be given opportunities to kill multiple birds with the same stone - ideally, they should be forced to use multiple stones per bird.

##### Originality

The effort required to reverse engineer an application highly depends on how much information is initially available to the adversary. This includes information about the functionality being reversed as well as knowledge about the obfuscation and anti-tampering techniques used by the target application. Therefore, the level of innovation that went into designing anti-reversing tricks is an important factor.

Adversaries are more likely to be familiar with ubiquitous techniques that are repeatedly documented in reverse engineering books, papers, presentations and tutorials. Such tricks can either be bypassed using generic tools or with little innovation. In contrast, a secret trick that hasn't been presented anywhere can only be bypassed by a reverser who truly understands the subject, and may force them to do additional research and/or scripting/coding.

Defenses can be roughly categorized into the following categories in terms of originality:

- Standard API: The feature relies on APIs that are specifically meant to prevent reverse engineering. It can be bypassed easily using generic tools.
- Widely known: A well-documented and commonly used technique is used. It can be bypassed using commonly available tools with a moderate amount of customization.
- Proprietary: The feature is not commonly found in reversing resources and research papers, or a known technique has been sufficiently extended / customized to cause significant effort for the reverse engineer.

##### API Layer

Generally speaking, the less your mechanisms relies on operating system APIs to work, the more difficult it is to discover and bypass. Also, lower-level calls are more difficult to defeat than higher level calls. To illustrate this, let's have a look at a few examples.

As you have learned in the 


```c
#define PT_DENY_ATTACH 31

void disable_gdb() {
    void* handle = dlopen(0, RTLD_GLOBAL | RTLD_NOW);
    ptrace_ptr_t ptrace_ptr = dlsym(handle, "ptrace");
    ptrace_ptr(PT_DENY_ATTACH, 0, 0, 0);
    dlclose(handle);
}
```

```c
void disable_gdb() {

	asm(
		"mov	r0, #31\n\t"	// PT_DENY_ATTACH
		"mov	r1, #0\n\t"
		"mov	r2, #0\n\t"
		"mov 	ip, #26\n\t"	// syscall no.
		"svc    0\n"
	);
}
```

```c
struct VT_JdwpAdbState *vtable = ( struct VT_JdwpAdbState *)dlsym(lib, "_ZTVN3art4JDWP12JdwpAdbStateE");

	unsigned long pagesize = sysconf(_SC_PAGE_SIZE);
	unsigned long page = (unsigned long)vtable & ~(pagesize-1);

	mprotect((void *)page, pagesize, PROT_READ | PROT_WRITE);

	vtable->ProcessIncoming = vtable->ShutDown;

	// Reset permissions & flush cache

	mprotect((void *)page, pagesize, PROT_READ);
```

- System library: The feature relies on public library functions or methods.
- System call: The anti-reversing feature calls directly into the kernel. 
- Self-contained: The feature does not require any library or system calls to work.


##### Parallelism

Debugging and disabling a mechanism becomes more difficult when multiple threats or processes are involved.

- Single thread 
- Multiple threads or processes

--[ TODO - description and examples ] --

<img src="Images/Chapters/0x07b/multiprocess-fork-ptrace.png" width="500px" />


##### Response Type

Less is better in terms of information given to the adversary. This principle also applies to anti-tampering controls: A control that reacts to tampering immediately in a visible way is more easily discovered than a control that triggers some kind of hidden response with no apparent immediate consequences. For example, imagine a debugger detection mechanism that displays a message box saying "DEBUGGER DETECTED!" in big, red, all-caps letters. This gives away exactly what has happened, plus it gives the reverse engineer something to look for (the code displaying the messagebox). Now imagine a mechanism that quietly changes modifies function pointer when it detects a debugger, triggering a sequence of events that leads to a crash later on. This makes the reverse engineering process much more painful.

The most effective defensive features are designed to respond in stealth mode: The attacker is left completely unaware that a defensive mechanism has been triggered. For maximum effectiveness, we recommend mixing different types of responses including the following:

- Feedback: When the anti-tampering response is triggered, an error message is displayed to the user or written to a log file. The adversary can immediately discern the nature of the defensive feature as well as the time at which the mechanism was triggered.
- Indiscernible: The defense mechanism terminates the app without providing any error details and without logging the reason for the termination. The adversary does not learn information about the nature of the defensive feature, but can discern the approximate time at which the feature was triggered.
- Stealth: The anti-tampering feature either does not visibly respond at all to the detected tampering, or the response happens with a significant delay. 

See also MASVS V8.8: "The app implements multiple different responses to tampering, debugging and emulation, including stealthy responses that don't simply terminate the pap."

#### Scattering

--[ TODO ] --

#### Integration

--[ TODO ] --

### Obfuscation

Elaborate obfuscation schemes, such as custom implementations of white-box cryptography or virtual machines, are better assessed in an isolated fashion using the white-box approach. Such an assessment requires specialized expertise in cracking the particular type(s) of obfuscation. In this type of assessment, the goal is to determine resilience against current state-of-the-art de-obfuscation techniques, and providing an estimate of robustness against manual analysis.

- Programmatic defense is a fancy word for "anti-reversing-trick".  for  are For a protection scheme to be considered effective, it must incorporate many of these defenses. "Programmatic" refers to the fact that these kinds of defenses *do* things - they are functions that prevent, or react to, actions of the reverse engineer. In this, they differ from obfuscation, which changes the way the program looks. 

- Obfuscation is the process of transforming code and data in ways that make it more difficult to comprehend, while preserving its original meaning or function. Think about translating an English sentence into an French one that says the same thing (or pick a different language if you speak French - you get the point).

Note that these two categories sometimes overlap - for example, self-compiling or self-modifiying code, while most would refer to as a means of obfuscation, could also be said to "do something". In general however it is a useful distinction.

Programmatic defenses can be further categorized into two modi operandi:

1. Preventive: Functions that aim to *prevent* anticipated actions of the reverse engineer. As an example, an app may use an operating system API to prevent debuggers from attaching.

2. Reactive: Features that aim to detect, and respond to, tools or actions of the reverse engineer. For example, an app could terminate when it suspects being run in an emulator, or change its behavior in some way if a debugger is detected.

You'll usually find a mix of all the above in a given software protection scheme.



#### Coverage




```
8.1 The app detects, and responds to, the presence of a rooted or jailbroken device either by alerting the user or terminating the app.
```


```
8.2: The app implements prevents debugging and/or detects, and responds to, a debugger being attached. All available debugging protocols must be covered (Android: JDWP and ptrace, iOS: Mach IPC and ptrace).
```


```
8.3: The app detects, and responds to, tampering with executable files and critical data within its own container.
```


```
8.4: The app detects the presence of widely used reverse engineering tools and frameworks that support code injection, hooking, instrumentation and debugging.
```


```
8.5: The app detects, and responds to, being run in an emulator.
```

```
8.6: The app continually verifies the integrity of critical code and data structures within its own memory space.
``` 



## Assessing Obfuscation

The simplest way of making code less comprehensible is stripping information that is meaningful to humans, such as function and variable names. Many more intricate ways have been invented by software authors - especially those writing malware and DRM systems - over the past decades, from encrypting portions of code and data, to self-modifying and self-compiling code.

A standard implementation of a cryptographic primitive can be replaced by a network of key-dependent lookup tables so the original cryptographic key is not exposed in memory ("white-box cryptography"). Code can be into a secret byte-code language that is then run on an interpreter ("virtualization"). There are infinite ways of encoding and transforming code and data!

Things become complicated when it comes to pinpointing an exact academical definition. In [an often cited paper](https://www.iacr.org/archive/crypto2001/21390001.pdf "On the (Im)possibility of Obfuscating Programs"), Barak et. al describe the black-box model of obfuscation. The black-box model considers a program P' obfuscated if any property that can be learned from P' can also be obtained by a simulator with only oracle access to P. In other words, P’ does not reveal anything except its input-output behavior. The authors also show that obfuscation is impossible given their own definition by constructing an un-obfuscable family of programs.

Does this mean that obfuscation is impossible? Well, it depends on what you obfuscate and how you define obfuscation. Barack’s result only shows that *some* programs cannot be obfuscated - but only if we use a very strong definition of obfuscation. Intuitively, most of us know from experience that code can have differing amounts of intelligibility and that understanding the code becomes harder as code complexity increases. Often enough, this happens unintentionally, but we can also observe that implementations of obfuscators exist and are [more or less successfully used in practice](http://roar.uel.ac.uk/1061/1/Ceccato,%20M%20(2008)%20QoP%2039%20.pdf "Towards Experimental Evaluation of Code Obfuscation Techniques").

Unfortunately, researchers don't agree on whether obfuscation effectiveness can ever be proven or quantified, and there are no widely accepted methods of doing it. In the following sections, we provide a taxonomy of commonly used types of obfuscation. We then outline the requirements for achieving what we would consider *robust* obfuscation, given the de-obfuscation tools and research available at the time of writing. Note however that the field is rapidly involving, so in practice, the most recent developments must always be taken into account.

### Obfuscation Controls in the MASVS

The [MASVS](https://github.com/OWASP/owasp-masvs/blob/master/Document/0x15-V8-Resiliency_Against_Reverse_Engineering_Requirements.md "V8: Resilience Requirements") lists only two requirements that deal with obfuscation. The first requirement is V8.12:

> 8.12 All executable files and libraries belonging to the app are either encrypted on the file level and/or important code and data segments inside the executables are encrypted or packed. Trivial static analysis does not reveal important code or data.

This requirement simply says that the code should be made to look fairly incomprehensible to someone inspecting it in a common disassembler or decompiler. This can be achieved by doing a combination of the following.

**Stripping information**

The first simple and highly effective step involves stripping any explanative information that is meaningful to humans, but isn’t actually needed for the program to run. Debugging symbols that map machine code or byte code to line numbers, function names and variable names are obvious examples.

For instance, class files generated with the standard Java compiler include the names of classes, methods and fields, making it trivial to reconstruct the source code. ELF and Mach-O binaries have a symbol table that contains debugging information, including the names of functions, global variables and types used in the executable.

Stripping this information makes a compiled program less intelligible while fully preserving its functionality. Possible methods include removing tables with debugging symbols, or renaming functions and variables to random character combinations instead of meaningful names. This process sometimes reduces the size of the compiled program and doesn’t affect its runtime behavior.

**Packing, encryption, and other tricks**

In addition to stripping information, there's many ways of making apps difficult and annoying to analyze, such as:

- Splitting up code and data between Java bytecode and native code; 
- Encrypting strings;
- Encrypting parts of the code and data withing the program;
- Encrypting whole binary files and class files.

This kind of transformations are "cheap" in the sense that they don't add significant runtime overhead. They form a part of every effective software protection scheme, no matter the particular threat model. The goal is simply to make it hard to understand what is going on, adding to the overall effectiveness of the protections. Seen in isolation, these techniques are not highly resilient against manual or automated de-obfuscation.

The second requirement, V8.13, deals with cases where obfuscation is meant to perform a specific function, such as hiding a cryptographic key, or concealing some portion of code that is considered sensitive.

> 8.13: If the goal of obfuscation is to protect sensitive computations, an obfuscation scheme is used that is both appropriate for the particular task and robust against manual and automated de-obfuscation methods, considering currently published research. The effectiveness of the obfuscation scheme must be verified through manual testing. Note that hardware-based isolation features should preferred over obfuscation whenever possible.

This is where more "advanced" (and often controversial) forms of obfuscation, such as white-box cryptography, come into play. This kind of obfuscation is meant to be truly robust against both human and automated analysis, and usually increases the size and complexity of the program. The methods aim to hide the semantics of a computation by computing the same function in a more complicated way, or encoding code and data in ways that are not easily comprehensible.

A simple example for this kind of obfuscations are opaque predicates. Opaque predicates are redundant code branches added to the program that always execute the same way, which is known a priori to the programmer but not to the analyzer. For example, a statement such as if (1 + 1) = 1 always evaluates to false, and thus always result in a jump to the same location. Opaque predicates can be constructed in ways that make them difficult to identify and remove in static analysis.

Other obfuscation methods that fall into this category are:

- Pattern-based obfuscation, when instructions are replaced with more complicated instruction sequences
- Control flow obfuscation
- Control flow flattening
- Function Inlining
- Data encoding and reordering
- Variable splitting
- Virtualization
- White-box cryptography

### Obfuscation Effectiveness

To determine whether a particular obfuscation scheme is depends on the exact definition of "effective". If the purpose of the scheme is to deter casual reverse engineers, a mixture of cost-efficient tricks is sufficient. If the purpose is to achieve a level of resilience against advanced analysis performed by skilled reverse engineers, the scheme must achieve the following:

1. Potency: Program complexity must increased by a sufficient amount to significantly impede human/manual analysis. Note that there is always a trade off between complexity and size and/or performance.
2. Resilience against automated program analysis. For example, if the type of obfuscation is known to be "vulnerable" to concolic analysis, the scheme must include transformations that cause problems for this type of analysis.

#### General Criteria

--[ TODO - describe effectiveness criteria ] --

**Increase in Overall Program Complexity**

--[ TODO ] --

**Difficulty of CFG Recovery**

--[ TODO ] --

**Resilience against Automated Program Analysis**

--[ TODO ] --

#### The Use of Complexity Metrics

--[ TODO  - what metrics to use and how to apply them] --

#### Common Transformations

--[ TODO  - describe commonly used schemes, and criteria associated with each scheme. e.g., white-box must incorportate X to be resilient against DFA,  etc.] --

##### Control-flow Obfuscation

--[ TODO ] --

##### Polymorphic Code

--[ TODO ] --

##### Virtualization

--[ TODO ] --

##### White-box Cryptography

--[ TODO ] --

## Background and Caveats

--[ TODO ] --

### Academic Research on Obfuscation Metrics

[Collberg et al.](https://researchspace.auckland.ac.nz/bitstream/handle/2292/3491/TR148.pdf "A taxonomy of obfuscating transformations") introduce potency as an estimate of the degree of reverse engineering difficulty. A potent obfuscating transformation is any transformation that increases program complexity. Additionally, they propose the concept of resilience which measures how well a transformation holds up under attack from an automatic de-obfuscator. The same paper also contains a useful taxonomy of obfuscating transformations.

Potency can be estimated using a number of methods. [Anaeckart et al.](https://biblio.ugent.be/publication/416824/file/448118.pdf "Program Obfuscation: A Quantitative Approach") apply traditional software complexity metrics to a control flow graphs generated from executed code. The metrics applied are instruction count, cyclomatic number (i.e. number of decision points in the graph) and knot count (number of crossing in a function’s control flow graph). Simply put, the more instructions there are, and the more alternate paths and less expected structure the code has, the more complex it is.

[Jacubowsky et al.](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/jakubowski09complex.pdf "Iterated transformations and quantitative metrics for software protection") use the same method and add further metrics, such as number of variables per instruction, variable indirection, operational indirection, code homogeneity and dataflow complexity. Other complexity metrics such as [N-Scope](http://adt.ivknet.de/download/papers/SCMM_rcomplexity.pdf "Software Complexity: Measures and Methods"), which is determined by the nesting levels of all branches in a program, can be used for the same purpose.

All these methods are more or less useful for approximating the complexity of a program, but they don’t always accurately reflect the robustness of the obfuscating transformations. [Tsai et al.](https://ir.nctu.edu.tw/bitstream/11536/7155/1/000266330500008.pdf "A Graph Approach to Quantitative Analysis of Control-Flow Obfuscating Transformations") attempt to remediate this by adding a distance metric that reflects the degree of difference between the original program and the obfuscated program. Essentially, this metric captures how the obfuscated call graph differs from the original one. Taken together, a large distance and potency is thought to be correlated to better robustness against reverse engineering.

In the same paper, the authors also make the important observation is that measures of obfuscation express the relationship between the original and the transformed program, but are unable to quantify the amount of effort required for reverse engineering. They recognize that these measure can merely serve as heuristic, general indicators of security.

Taking a human-centered approach, [Tamada et al.](http://tamadalab.github.io/papers/resources/tamada12snpd.pdf "Program Incomprehensibility Evaluation for Obfuscation Methods with Queue-based Mental Simulation Model") describe a mental simulation model to evaluate obfuscation. In this model, the short-term memory of the human adversary is simulated as a FIFO queue of limited size. The authors then compute six metrics that are supposed to reflect the difficulty encountered by the adversary in reverse engineering the program. Nakamura et. al. propose similar metrics reflecting the cost of mental simulation.

More recently, [Rabih Mosen and Alexandre Miranda Pinto proposed](https://pdfs.semanticscholar.org/6f92/50fb34a5b02b351e62e6cef85b0649c5ed85.pdf "Algorithmic Information Theory for Obfuscation Security") the use of a normalized version of Kolmogorov complexity as a metric for obfuscation effectiveness. The intuition behind their approach is based on the following argument: if an adversary fails to capture some patterns (regularities) in an obfuscated code, then the adversary will have difficulty comprehending that code: it cannot provide a valid and brief, i.e., simple description. On the other hand, if these regularities are simple to explain, then describing them becomes easier, and consequently the code will not be difficult to understand. The authors also provide empirical results showing that common obfuscation techniques managed to produce a substantial increase in the proposed metric. They found that the metric was more sensitive then Cyclomatic measure at detecting any increase in complexity comparing to original un-obfuscated code.

This makes intuitive sense and even though it doesn’t always hold true, the Kolmogorov complexity metric appears to be useful to quantify the impact of control flow and data obfuscation schemes that add random noise to a program.

### Experimental Data

With the limitations of existing complexity measures in mind we can see that more human studies on the subject would be helpful. Unfortunately, the body of experimental research is relatively small - in fact, [the lack of empirical studies](http://selab.fbk.eu/ceccato/papers/2014/ssp2014.pdf "On the Need for More Human Studies to Assess Software Protection") is one of the main issues researchers face. There are however some interesting papers linking some types of obfuscation to higher reverse engineering difficulty.

[Nakamura et al.](https://www.researchgate.net/profile/Akito_Monden/publication/4035136_Queue-based_cost_evaluation_of_mental_simulation_process_in_program_comprehension/links/0912f50ad85dc1b84b000000.pdf "Queue-based Cost Evaluation of Mental Simulation Process in Program Comprehension") performed an empirical study to investigate the impact of several novel cost metrics proposed in the same paper. In the experiment, twelve subjects were asked to mentally execute two different versions (with varying complexity) of three Java programs. At specific times during the experiment, the subjects were required to describe the program state (i.e., values of all variables in the program). The accuracy and speed of the participants in performing the experiment was then used to assess the validity of the proposed cost metrics. The results demonstrated that the proposed complexity metrics (some more than others) were correlated with the time needed by the subjects to solve the tasks. 

[Sutherland et al.](https://www.sjoerdlangkemper.nl/papers/2006/an-empirical-examination-of-the-reverse-engineering-process-for-binary-files-sutherland.pdf "An empirical examination of the reverse engineering process for binary files") examine a framework for collecting reverse engineering measurement and the execution of reverse engineering experiments. The researchers asked a group of ten students to perform static analysis and dynamic analysis on several binary programs and found a significant correlation between the skill level of the students and the level of success in the tasks (no big surprise there, but let’s count it as preliminary evidence that luck alone won’t get you far in reverse engineering).

In a series of [controlled](http://roar.uel.ac.uk/1061/1/Ceccato,%20M%20(2008)%20QoP%2039%20.pdf "Towards Experimental Evaluation of Code Obfuscation Techniques") [experiments](http://roar.uel.ac.uk/691/1/Ceccato%2C%20M.%20%282009%29%20ICPC%20178-87.pdf "The Effectiveness of Source Code Obfuscation: an Experimental Assessment"), M. Ceccato et al. tested the impact of identifier renaming and opaque predicates to increase the effort needed for attacks. In these studies, Master and PhD students with a good knowledge of Java programming were asked to perform understanding tasks or change tasks on the decompiled (either obfuscated or clear) client code of client-server Java applications. The experiments showed that obfuscation reduced the capability of subjects to understand and modify the source code. Interestingly, the results also showed that the presence of obfuscation reduced the gap between highly skilled attackers and low skilled ones: The highly skilled attackers were significantly faster in analyzing the clear source code, but the difference was smaller when analyzing the obfuscated version. Among other results, identifier renaming [was shown](http://roar.uel.ac.uk/691/1/Ceccato%2C%20M.%20%282009%29%20ICPC%20178-87.pdf "The Effectiveness of Source Code Obfuscation: an Experimental Assessment") to at least double the time needed to complete a successful attack.

<img src="Images/Chapters/0x07b/boxplot.png" width="650px" />

*Boxplot of attack efficiency from the Ceccato et. al. experiment to measure the impact of identifier renaming on program comprehension. Subjects analyzing the obfuscated code gave less correct answers per minute.*

### The Device Binding Problem

In many cases it can be argued that obfuscating some secret functionality misses the point, as for all practical purposes, the adversary does not need to know all the details about the obfuscated functionality. Say, the function of an obfuscated program it to take an input value and use it to compute an output value in an indiscernible way (for example, through a cryptographic operation with a hidden key). In most scenarios, the adversaries goal would be to replicate the functionality of the program – i.e. computing the same output values on a system owned by the adversary. Why not simply copy and re-use whole implementation instead of painstakingly reverse engineering the code? Is there any reason why the adversary needs to look inside the black-box?

This kind of attack is known as code lifting and is commonly used for breaking DRM and [white-box cryptographic implementations](http://newspaper23.com/ripped/2014/11/http-_____-___-_www___-whiteboxcrypto___-com__-_files__-_2012_misc.pdf "White-box cryptography: hiding keys in software"). For example, an adversary aiming to bypass digital media usage could simply extract the encryption routine from a player and include it in a counterfeit player, which decrypts the digital media without enforcing the contained usage policies [TODO](http://www.cs.rhul.ac.uk/home/kinder/papers/csur16-obfuscation.pdf "Protecting Software through Obfuscation: Can It Keep Pace with Progress in Code Analysis?"). Designers of white-box implementations have to deal with [another issue](https://eprint.iacr.org/2015/753.pdf "Differential Computation Analysis: Hiding your White-Box Designs is Not Enough"): one can convert an encryption routine into a decryption routine without actually extracting the key.

Protected applications must include measures against code lifting to be useful. In practice, this means binding the obfuscated functionality to the specific environment (hardware, device or client/server infrastructure) in which the binary is executed. Preferably, the protected functionality should execute correctly only in the specific, legitimate computing environment. For example, an obfuscated encryption algorithm could generate its key (or part of the key) using [data collected from the environment](https://pdfs.semanticscholar.org/1be5/425625872d882ea412b2c83b7cdf1d56da79.pdf "Environmental Key Generation towards Clueless Agents"). Techniques that tie the functionality of an app to specific hardware are known as device binding.

Even so, it is relatively easy (as opposed to fully reverse engineering the black-box) to monitor the interactions of an app with its environment. In practice, simple hardware properties such as the IMEI and MAC address of a device are often used to achieve device binding. The effort needed to spoof these environmental properties is certainly lower than the effort required for needed for fully understanding the obfuscated functionality.

What all this means is that, for most practical purposes, the security of an obfuscated application is only as good as the device binding it implements. For device binding to be effective, specific characteristics of the system or device must be deeply intertwined with the various obfuscation layers, and these characteristics must be determined in stealthy ways (ideally, by reading content directly from memory). Advanced device binding methods are often deployed in DRM and malware and [some research](https://www.researchgate.net/profile/Chengyu_Song/publication/267959563_Flowers_for_Automated_Malware_Analysis/links/55f6f76908aeafc8abf55d24.pdf "Flowers for Automated Malware Analysis") has been published in this area.
