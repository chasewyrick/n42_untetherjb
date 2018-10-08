**derebusantiquis**<br>
original: https://xerub.github.io/ios/iboot/2018/05/10/de-rebus-antiquis.html


ramdiskF2.dmg: Load /iBEC after exploit.<br>
<br>
**Additional patches**<br>
(1)<br>
**There may be a better way to do this**<br>
Call "/iBEC loading function" after "boot failure count".<br>

bff00e02         ldr        r2, [sp, arg_0]<br>
bff00e04         ldr        r1, [sp, arg_4]
bff00e06         ldr        r0, =0xbff34e78                                     ; "Boot Failure Count: %ld\\tPanic Fail Count: %ld\\n"<br>
bff00e08         bl         sub_bff33754 -> sub_bff013b8<br>
<br>
(2)<br>
boot-partition=0 patch<br>
<br>
             sub_bff013b8:<br>
bff013b8         push       {r4, r7, lr}                                        ; CODE XREF=sub_bff00d88+128<br>
bff013ba         add        r7, sp, #0x4<br>
bff013bc         sub        sp, #0x18<br>
bff013be         mov.w      r2, #0x84000000<br>
bff013c2         ldr        r0, =0xbff34cfc                                     ; "boot-partition"<br>
bff013c4         ldr        r1, =0xbff3522c<br>
bff013c6         movs       r4, #0x0<br>
...<br>
<br>
bff3522c         db  0x32 ; '2' -> '0'                                                ; DATA XREF=sub_bff013b8+12, dword_bff01438<br>
bff3522d         db  0x00 ; '.'<br>
<br>
**Untethered Boot**<br>
(1)<br>
Set /iBEC to the root of disk0s1s1 of the device.<br>
iBoot jumps to iBEC after exploit invocation.<br>
<br>
(2)<br>
iBEC loads /applelogo, /devicetree, /ramdisk, /kernelcache from boot-partition=0 at the OTA routine and untethered boot it.
(Things like CoolBooter)<br>
<br>
#Method of applying<br>
dd of=/dev/rdisk0s1s3 if=ramdiskF2.dmg bs=512k count=1<br>
nvram boot-partition=2<br>
nvram boot-ramdisk="/a/b/c/d/e/f/g/h/i/j/k/l/m/disk.dmg"<br>
<br>
and, install /iBEC, /applelogo, /devicetree, /ramdisk, /kernelcache<br>
<br>
#device<br>
iPhone5,2 iOS 7.0.4 iBoot only<br>
<br>
https://www.youtube.com/watch?v=dSfPt_vCA4I<br>
<br>
At your own risk!<br>
<br>
