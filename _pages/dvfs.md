# sysfs for power gating and dvfs
## part1:
    power_mode（power、clock的3d、vcp、vpp   进行auto、manual、close的切换）
			zx_params.c用一个16位的bits zx_pwm_mode表示：
												bitmap:pg_manual     pg_auto      cg_manual     cg_auto
												0 vpp vcp gfx|0 vpp vcp gfx|0 vpp vcp gfx |0 vpp vcp gfx
    查看：cat zx_info/power_mode
	更改：1、echo bitmaps //前缀字符
			例子：echo 0x61 > zx_info/power_mode
            2、格式化 echo 		gating_type        engine       state   > zx_info/pg_cg_gating_mode     
						0    power:
									0(  3d)      0(  close)
									1(  vcp)     1(  auto)
									2(  vpp)     2(  manual)
						1    clock:
									0(  3d)      0(  close)
									1(  vcp)     1(  auto)
									2(  vpp)     2(  manual)
						
						only support:
						1.pg close - cg close 
						2.pg close - cg auto 
						3.pg manual - cg auto 
						4.pg manual - cg manual
        例子：想要将pg 的 3d gating关掉:   echo 0 0 0 > zx_info/pg_cg_gating_mode
        全部关闭： echo 0 > zx_info/power_mode
	警告：推荐用方式1更改，用方式2必须保证任何时刻的状态都属于受支持的四种状态，否则就会hang。

	
## part2:
	pwm_wait_count （设置调节的idle时间）
	查看：cat zx_info/pwm_wait_count
	更改：1、将cat输出的第一行更改，然后回写
			例子：将pg_vpp改为195   echo  pg_3d:20,cg_3d:200,pg_vcp:20,cg_vcp:200,pg_vpp:195,cg_vpp:200
					...
		  2、格式化 echo 	gating_type        engine      hex-value(~)：16进制
							0    power_wait:
											   0(  3d)      10~200
											   1(  vcp)     10~200
											   2(  vpp)     10~200
							1    clock_wait:
											   0(  3d)      10~200
											   1(  vcp)     10~200
											   2(  vpp)     10~200
        例子：将pg_vpp改为180:   echo 0 2 180 > zx_info/pwm_wait_count
			
			
## part3：
	dvfs_mode  
	查看：cat zx_info/dvfs_mode
	更改：同part1

      例子：
      1、bitmap      dvfs force     dvfs auto
            echo     0 vpp vcp gfx | 0 vpp vcp gfx
        echo 0x10

      2、	For change value,you can directly return the 8-bitmap or format echo:
             engine         mode
             0(  3d)      0(  close)
             1(  vcp)     1(  auto)
             2(  vpp)     2(  manual)
        切换3d dvfs到force/manual:  echo 0 2 > zx_info/dvfs_mode

	
## part4：
	dvfs_control_params
    查看：cat zx_info/dvfs_control_params
    更改：同part2 pwm_wait_count

      例子：gfx_dvfs_checksize:0,gfx_dvfs_h_threshold:0,gfx_dvfs_l_threshold:0,vcp0_dvfs_checksize:0,vcp0_dvfs_h_threshold:0,vcp0_dvfs_l_threshold:0,vcp1_dvfs_checksize:0,vcp1_dvfs_h_threshold:0,vcp1_dvfs_l_threshold:0,vpp_dvfs_checksize:0,vpp_dvfs_h_threshold:0,vpp_dvfs_l_threshold:0
            For change value, you can directly return the above line or format echo:
            engine              dvfs_params       hex-value(~)：16进制
            0    3d:
                               0(  checksize)      0~0xffffffff
                               1(  h_threshold)    0~0xffffffff
                               2(  l_threshold)    0~0xffffffff
            1    vcp0:
                               0(  checksize)      0~0xffffffff
                               1(  h_threshold)    0~0xffffffff
                               2(  l_threshold)    0~0xffffffff
            2    vcp1:
                               0(  checksize)      0~0xffffffff
                               1(  h_threshold)    0~0xffffffff
                               2(  l_threshold)    0~0xffffffff
            3    vpp:
                               0(  checksize)      0~0xffffffff
                               1(  h_threshold)    0~0xffffffff
                               2(  l_threshold)    0~0xffffffff

									   
## part5:
	dvfs_clk_voltage   tips:调电压、频率要在dvfs manual模式下 输入电压=实际电压*1000
    查看：cat zx_info/dvfs_clk_voltage 
    更改：parameter        engine       range(~)：10进制
         c    clock(MHz):                        
                           0(  3d)       200 ~ 625
                           1(  vcp0)     200 ~ 625
                           2(  vcp1)     200 ~ 625
                           3(  vpp)      200 ~ 625
         v    vlotage(mV):
                           0(  3d)       (0.800 ~ 1.520)*1000
                           1(  vcp0)     (0.800 ~ 1.520)*1000
                           2(  vcp1)     (0.800 ~ 1.520)*1000
                           3(  vpp)      (0.800 ~ 1.520)*1000
     例子：调电压 echo v 0 845 > zx_info/dvfs_clk_voltage

			 
## part6:
    dvfs_level
    查看：cat zx_info/dvfs_level
        nopower
            you can input one of [balance, nopower, performance]
        
    更改：echo [balance, nopower, performance] > zx_info/dvfs_level
	