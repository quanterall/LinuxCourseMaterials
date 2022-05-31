# Линукс и началото


Иван И. 

Какво е Linux?
Linux е операционна система, базирана на UNIX OS. 
Всъщност  Linux, една от най-популярните платформи на планетата, Android, се захранва от операционната система Linux, както и много други. 
Операционната система е софтуер, който управлява всички хардуерни ресурси, свързани с вашия работен плот или лаптоп. Казано по-просто, операционната система управлява комуникацията между вашия софтуер и хардуер. Без операционната система (ОС) софтуерът няма да функционира.

Защо Linux?
Absolutely Free
Safe and Secure
Generally less vulnerable to ransomware,
malware or viruses
More Lightweight
Thousands of applications for various needs
Customization
A Linux server can go years without being
rebooted. If you follow regular recommended
updates, stability and dependability are
practically assured


Boot Process part 1
1.BIOS = Basic input/output system. It performs system integrity checks. Searches, loads and executes boot loader program, disk drives, SD card reader, CD/DVD ROM, HARD DRIVE.
2. MBR = Master Boot Record. It is located in the first sector of the bootable disk. Usually in /dev/hda or /dev/sda. It's less than 512 bytes in size. This has 3 components = Primary boot loader info in the first 446 bytes, Partition table info in the next 64 bytes, MBR Validation check in last 2 bytes.
3.GRUB = Grand Unified Bootloader. If you have multiple kernel images in stool then you can choose which one to be executed. It displays a splash screen and waits for a few seconds.
If nothing is entered, it loads the default kernel image as specified GRUB configuration file. GRUB has knowledge of a file system. The configuration file is called Grub.
/boot/grub/grub.conf   (In short, Grub loads and executes Karnel) and (init Rd/Initial ramdisk).







Boot process part 2.
4. Init RD = is used by the Kernel as a temporary root filesystem, until Kernel is booted in a real root filesystem is mounted.
5. Karnel = it mounts the root filesystem. The Kernel executes the init program (located in /sbin/init folder). Init is the first program to be executed, it gets a process ID (PID) of 1.
 You can run PS command to verify. (In short, Kernel loads the filesystem and runs the initial program).
6. Init (Initialization) = Looks at the /etc/inittab to decide the linux run level



Following are the available run levels
0 – System halt i.e the system can be safely powered off with no activity.
1 – Single user mode. (Used for system maintenance No networking capabilities).
2 – Multiple user mode with no NFS(network file system) (Used for maintenance and system testing).
3 – Multiple user mode under the command line interface and not under the graphical user interface (GUI).
4 – User-definable. (Custom Mode, used by SysAdmin)
5 – (X11) Multiple user mode under GUI (graphical user interface) and this is the standard runlevel for most of the LINUX based systems.
6 – Reboot which is used to restart the system.(shuts down all services when the system is being rebooted).


The directories for each level
Run level0 - /etc/rc.d/rc0.d
Run level1 - /etc/rc.d/rc1.d
Run level2 - /etc/rc.d/rc2.d
Run level3 - /etc/rc.d/rc3.d
Run level4 - /etc/rc.d/rc4.d
Run level5 - /etc/rc.d/rc4.d
Run level6 - /etc/rc.d/rc4.d
Under the /etc/rc.d/rc*.d/directories, 
you would see programs that start with S and K. 
Programs used with S => are used during startup
Programs used with K => are used during shutdown


Linux Distros / Линукс Дистрибуции
Линукс има редица различни версии, подходящи за всеки тип потребител.
От нови потребители до hardcore такива, ще намерите Linux версия, която да отговаря на вашите нужди.
Тези версии се наричат дистрибуции или по-кратко “distros”. Почти всяка Linux дистрибуция може да бъде изтеглена безплатно, записана на диск или USB устройство и инсталирана на колкото искате машини.



По-популярните Linux дистрибуции включват:
• Debian
• Ubuntu
• Linux Mint
• Fedora
• OpenSUSE
• Solus
• Antergos
• Elementary OS


Сървърните дистрибуции / Enterprise level
• Red Hat Enterprise Linux
• Ubuntu Server
• CentOS
• SUSE Enterprise Linux



Linux File Hierarchy Structure (FHS)
1. “/” (root) – root е основната директория на
цялата йерархия на файловата система в Linux
• Всеки отделен файл и директория започва от
основната директория
• Единствено root потребителя има право да
пише под тази директория
• /root е домашната директория на root
потребителя, която не е същата, като /


/bin
2. /bin – Основни command binaries, които трябва
да са налични в single-user режим. За всички
пторебители – например cat, ls, cp
• Съдържа binary executable файлове
• Основни linux команди, които са нужни на
всички потребители на системата – ls, ping,
grep и др.


/boot
/boot – Bootloader файлове, като kernels

• kernel initrd, vmlinux, grub файловете се
намират в /boot
• пример: vmlinux2.6.32.24-generic


/dev
/dev – Есенциални device файлове, като
/dev/null
• Включват терминални устройства, usb или
всякакви други устройства, закачени за
системата
• пример: /dev/tty1, /dev/usbmon0


/etc
/etc – Host-specific конфигурационни файлове
за цялата система
• Съдържа конфигурационни файлове нужни
на всички програми
• Също съдържа startup и shutdown shell
скриптове използвани за start/stop на
индивидуални програми.
• Пример: /etc/resolv.conf, /etc/logrotate.conf


/home
/home – Домашната директория на
потребител. Съдържа запазени файлове,
персонални настройки и други подобни.


/lib
/lib – Библиотеки, есенциални за всички
binaries в /bin/ и /sbin/
• Library имената на файлове са или ld* или
lib*.so.*

• пример: ld-2.11.1.so, libncourses.so.5.7


/Media
/media – Mount points за removable медия,
като CD-ROM
• Временни mount директории за премахваеми
медийни източници
• примери: /media/cdrom, /media/floppy


/mnt
/mnt – Временно закачени файлови системи
• Временна mount директория, където
системните администратори могат да
“закачат” файлови системи.


/opt
/opt – Опционални софтуерни пакети
• Съдържа add-on приложения от различни
доставчици.


/sbin
/sbin – Есенциални системни библиотеки.
Fsck, init, route.
• Точно като /bin , /sbin също съдържа binary
executables.
• Линукс командите, намиращи се под тази
диркетория, се използват от системни
администратори за поддръжка на системи.
• Примери: iptables, reboot, fdisk, ifconfig


/srv
/srv – Специфични за сайт данни,
обслужвани от системата, като данни  и
скриптове за уеб сървъри, данни предлагани от
FTP сървъри и хранилища(repositories) за
системи за контрол на версии.
• Srv е съкратено от service
• Съдържа server specific services related данни
• пример: /srv/cvs съдържа данни свързани с
CVS


/tmp
/tmp – Временни файлове. Често не се
запазва след рестартиране на системата.


/usr  part 1
/usr – Вторична йерархия за потребителски
данни само за четене. Съдържа по-голямата
част от multi-user помощни програми и
приложения.
• Съдържа binaries, библиотеки, документация
и source-code за второстепенни програми.


/usr part 2 
• /usr/bin съдържа binary файлове за
потребителски програми.
• /usr/sbin съдържа binary файлове за
системно администриране.
• /usr/lib съдържа библиотеките за /usr/bin и
/usr/sbin
• /usr /local съдържа потребителски програми,
които сте инсталирали от източник.
Например, когато инсталирате apache от
източник, то отива в /usr/local/apache2
• /usr/src съдържа Linux kernel източници,
header файлове и документация.


/proc
Виртуална файлова система,
осигуряваща информация за процесите и
ядрото под формата на файл. В Linux,
кореспондира на procfs mount. Автоматично
генерирана и попълва от системата в движение.
Съдържа информация за системните процеси
Това е псевдо файлова система, съдържаща
информация за стартираните процеси
(running processes).  Пример в part 2 



/proc part 2

Пример: /proc/<PID>
директорията съдържа информация за
процеса с това Process ID
• Това е виртуална файлова система с
информация относно системните ресурси.
Пример: /proc/uptime

