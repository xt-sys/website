+++
title = 'Frequently Asked Questions'
date = 2024-06-16T12:33:36+02:00
+++
#### What is ExectOS?
ExectOS is an open-source, general purpose operating system written from scratch and based on new XT architecture.
It aims to be modular, maintainable and compatible with existing software.

#### What does ExectOS mean? What is the origin of this name?
The name 'ExectOS' doesn’t have a specific historical origin, as it appears to be a unique creation for this
Operating System. The term 'Exec' within ExectOS directly relates to execution - the act of carrying out tasks,
processes, and instructions. In the context of operating system, execution involves managing programs, resources,
and system operations. Users should associate ExectOS with streamlined performance and reliability.

#### What does the ExectOS logo present and what is the relation between the logo and the OS name?
The three wavy lines in the ExectOS logo evoke a sense of fluidity and movement. Their arrangement, forms a shape
reminiscent of the capital letter ‘E,’ directly corresponding to the initial letter of ExectOS. The flowing waves
symbolize dynamism, progress, and forward motion. The smooth curves suggest a seamless user experience, while gradient
colors represent diversity and adaptability. All of this convey a message of innovation, user-friendliness, and
adaptability. It invites users to explore a new operating system that promises a seamless computing experience.

#### What is the difference between XTOS and ExectOS?
XTOS is a common name of the Operating System standard based on the XT architecture. ExectOS is a name of the specific
project, that is a reference XTOS implementation, that implements all requirements from a corresponding specification.

#### What does XT mean?
XT is a name of XTOS architecture of an Operating System, that consists of two main components, user mode and kernel
mode. It derives mostly from NT&trade; architecture, as well as uses a couple of ideas from other open source projects.
XT is a short for "neXTgen" architecture, but you can also call it "eXTendable".

#### What architectures does ExectOS support?
ExectOS supports the x86_64 (sometimes called AMD64 or EM64T), as well as i686 CPU architectures. However, it requires
a modern EFI enabled hardware, thus it is not possible currently to boot ExectOS on a legacy BIOS.

#### Is ExectOS a Windows replacement?
No. ExectOS is a brand new, modern system designed to run applications written for many different types of Operating
Systems. This allows to implement any environment subsystem to support applications that are strictly written to the
corresponding standard (eg. DOS, or POSIX). This means that running Windows applications might be as well possible at
some time. However development is mostly focused on providing system core at the moment.

#### Is ExectOS another Unix based OS?
No. ExectOS implements the XT architecture which derives from NT&trade; architecture. It is modular, and consists of
two main layers: microkernel and user mode. Its' kernel mode has full access to the hardware and system resources and
runs code in a protected memory area. It consists of executive services, which is itself made up on many modules that
do specific tasks, a kernel and drivers. Unlike the NT&trade;, system does not feature a separate Hardware Abstraction
Layer (HAL) between the physical hardware and the rest of the OS. Instead, XT architecture integrates a hardware specific
code with the kernel.

#### Does ExectOS allow to use Windows drivers?
This is one of our goals. Thanks to the NT&trade; drivers compatibility layer provided by ExectOS, you should be able to
install and use the same drivers as you can on Windows&reg;.

#### Can I install ExectOS on a pendrive / USB stick?
Yes. This is how it is tested on a bare metal (real hardware).

#### Program X does not work when I launch it. When can I except things to change?
It depends. ExectOS is in early development stage and it is not recommended for everyday usage. At this point we cannot
guarantee anything.

#### When will feature X get added?
If you think an idea has merit, you may choose to discuss it on [Discord Server](https://discord.com/invite/zBzJ5qMGX7).
However, the best way to get a feature added to ExectOS is to implement it yourself.

#### When will the project be finished?
Writing an Operating System is a complex task that requires time. Other popular Operating Systems, such as Linux, are
also constantly being developed to meet the everyday users requirements.

#### Where can I obtain the source code?
ExectOS source code can be obtained from GIT repository available [here](https://git.codingworkshop.eu.org/xt-sys/exectos).
It can be also found on [GitHub](https://github.com/xt-sys/exectos) and [GitLab](https://gitlab.com/xt-sys/exectos) mirrors.

#### Where can I download ExectOS?
Each ExectOS build, including binaries, disk image, libraries and debug symbols are available
[here](https://artifacts.codingworkshop.eu.org/ExectOS/).

#### How can I contribute to ExectOS?
We are looking for C and Assembler developers for core OS development as well as testers. If you are willing to help,
please join our [Discord server](https://discord.com/invite/zBzJ5qMGX7) and contact one of ExectOS developers.

#### Why don't you use GCC?
Because GCC is a crap.

#### Do you have any kind of tests to check if the code is working as expected?
In the spirit of lighthearted development, we often quip "We don't need tests. If it compiles, it is good enough; if it
boots up, then it is perfect." However, on a more serious note, while our current focus is heavily on developing core
features and conducting manual testing, we fully recognize the importance of automated testing. As the project matures,
implementing a comprehensive suite of automated tests is definitely on our roadmap.

#### Why don't you help Wine?
Wine implements Win32Api only, while ExectOS is a featureful Operating System, that implements a compatibility layer with
NT&trade;. This means, ExectOS will be able to run NT&trade; drivers as well, not only Windows&reg; software. However,
thanks to its modular design, it will be possible to implement Win32 subsystem as well at some point, based on Wine.

#### Why don't you help ReactOS?
ExectOS goals are very different from ReactOS, and contrast the project's core philosophy as being quite on different
paths. While ReactOS aims to replicate Windows&reg; NT&trade;, ExectOS is a completely new Operating System implementing
the XT architecture which derives from NT&trade;. Although both projects share the goal of being NT&trade; compatible,
they intend to achieve it in different ways. What ReactOS tries to replicate, ExectOS only implements as a compatibility
layer. Thanks to that, ExectOS does not need to strictly follow NT&trade; architecture and is capable of providing modern
features.

#### Do you intend to cooperate with ReactOS to achieve common goals?
No. We share Wine's opinion on the inappropriate reverse-engineering methods used in the ReactOS project, as well as its
association with the TinyKrnl project, which used every possible method of achieving the end result of having a 100%
compatible results. This especially applies to the so-called ‘dirty’ way.

#### Is ExectOS an EU-funded project?
No. ExectOS is not funded by the European Union; however, it is a project led by Europeans committed to adhering to EU
laws and regulations. While the project has its roots in Europe, we wholeheartedly welcome all individuals around the
world. Everyone is invited to join, use, and contribute to ExectOS, fostering a truly global community.

#### Which license does ExectOS use?
ExectOS is licensed under the GNU General Public License v3.
