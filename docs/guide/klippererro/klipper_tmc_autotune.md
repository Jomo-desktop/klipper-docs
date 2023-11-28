# Klipper TMC 自动调谐

项目地址：[github](https://github.com/andrewmcgr/klipper_tmc_autotune)

Klipper 扩展，用于自动配置和调整 TMC 驱动程序。

此扩展为大多数 TMC 步进电机驱动器寄存器计算良好的值，给定电机的数据表信息和用户选择的调谐目标。

特别是，它默认在Z轴电机和挤出机上启用StealthChop，在可能的情况下启用CoolStep，并在非常高的速度下正确切换到全步操作。在可能有多种模式的情况下，它应该选择可用的最低功耗和最安静的模式，但要遵守无传感器归位的限制（不允许某些组合）。

### 现状

- 对 TMC2209、TMC2240 和 TMC5160 的官方支持。
- 对 TMC2130、TMC2208 和 TMC2660 的支持可能有效，但完全未经测试。
- 众所周知，启用自动调谐的无传感器归位适用于TMC2209、TMC2240和TMC5160，前提是您足够快地返回（对于使用无传感器归位的轴，homing_speed在数值上应大于 rotation_distance）。与往常一样，首次尝试无传感器归位时要非常小心。
- 对电机使用自动调谐可以让电机运行温度更低，消耗更少的功率，从而提高效率。但是，需要注意的是，此过程也可能导致 TMC 驱动程序运行得更热，因此必须实施适当的冷却措施。