add_compile_definitions(
    DELAY_MS=100
    IN_PIN=4
    OUT_PIN=7)

add_executable(task_delay task_delay.c)
pico_enable_stdio_uart(task_delay 0)
pico_enable_stdio_usb(task_delay 1)
target_link_libraries(task_delay PRIVATE
  pico_stdlib
  pico_multicore
  pico_async_context_freertos
  FreeRTOS-Kernel-Heap4
)
pico_add_extra_outputs(task_delay)
add_custom_target(flash_delay picotool load -f $<TARGET_FILE:task_delay> DEPENDS task_delay)


add_executable(timer timer.c)
pico_enable_stdio_uart(timer 0)
pico_enable_stdio_usb(timer 1)
target_link_libraries(timer PRIVATE pico_stdlib)
pico_add_extra_outputs(timer)
add_custom_target(flash_timer picotool load -f $<TARGET_FILE:timer> DEPENDS timer)


add_executable(sleep sleep.c)
pico_enable_stdio_uart(sleep 0)
pico_enable_stdio_usb(sleep 1)
target_link_libraries(sleep PRIVATE pico_stdlib)
pico_add_extra_outputs(sleep)
add_custom_target(flash_sleep picotool load -f $<TARGET_FILE:sleep> DEPENDS sleep)


add_executable(irq gpio_interrupt.c)
pico_enable_stdio_uart(irq 0)
pico_enable_stdio_usb(irq 1)
target_link_libraries(irq PRIVATE pico_stdlib)
pico_add_extra_outputs(irq)
add_custom_target(flash_irq picotool load -f $<TARGET_FILE:irq> DEPENDS irq)

# add_executable(rtc rtc.c)
# pico_enable_stdio_uart(rtc 0)
# pico_enable_stdio_usb(rtc 1)
# target_link_libraries(rtc PRIVATE pico_stdlib hardware_rtc pico_cyw43_arch_none)
# pico_add_extra_outputs(rtc)
# add_custom_target(flash_rtc picotool load -f $<TARGET_FILE:rtc> DEPENDS rtc)