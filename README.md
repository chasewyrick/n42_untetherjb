#derebusantiquis
ramdiskF2.dmg: Load /iBEC after exploit.

#Additional patches
1)
**There may be a better way to do this**
Call "/iBEC loading function" after "boot failure count".

bff00e02         ldr        r2, [sp, arg_0]
bff00e04         ldr        r1, [sp, arg_4]
bff00e06         ldr        r0, =0xbff34e78                                     ; "Boot Failure Count: %ld\\tPanic Fail Count: %ld\\n"
bff00e08         bl         sub_bff33754 -> sub_bff013b8

2)
boot-partition=0 patch

             sub_bff013b8:
bff013b8         push       {r4, r7, lr}                                        ; CODE XREF=sub_bff00d88+128
bff013ba         add        r7, sp, #0x4
bff013bc         sub        sp, #0x18
bff013be         mov.w      r2, #0x84000000
bff013c2         ldr        r0, =0xbff34cfc                                     ; "boot-partition"
bff013c4         ldr        r1, =0xbff3522c                                     
bff013c6         movs       r4, #0x0
...

bff3522c         db  0x32 ; '2' -> '0'                                                ; DATA XREF=sub_bff013b8+12, dword_bff01438
bff3522d         db  0x00 ; '.'

3)
Set /iBEC to the root of disk0s1s1 of the device.
iBoot jumps to iBEC after exploit invocation.

4)
iBEC loads /applelogo, /devicetree, /ramdisk, /kernelcache from boot-partition=0 at the OTA routine and untethered boot it.
(Things like CoolBooter)

#Method of applying
dd of=/dev/rdisk0s1s3 if=ramdiskF2.dmg bs=512k count=1
nvram boot-partition=2
nvram boot-ramdisk="/a/b/c/d/e/f/g/h/i/j/k/l/m/disk.dmg"

and, install /iBEC, /applelogo, /devicetree, /ramdisk, /kernelcache

#device
iPhone5,2 iOS 7.0.4 iBoot

https://www.youtube.com/watch?v=dSfPt_vCA4I

At your own risk!
