

# GART表






## ACE power gating
1. three register：gating ctrl、wait count、status show
2. ctrl target:hw(3D0/L2/VID<vcp>/VPP)、sw(add 3D<CMU>)
3. ctrl logic: hw first and will recover sw
4. ctrl case: atuo->sw <engine idle, auto enable, sw enbale, disable auto> due to auto first.
              sw->auto <sw to wanna mode, auto enable , sw disable> due to escape step signal
            
