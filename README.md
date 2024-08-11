# simulation based on ohms law
import matplotlib.pyplot as plt
import numpy as np
from mpl_toolkits.mplot3d import Axes3D
from matplotlib.widgets import Slider

def VoltageDividerBJT(vcc, R1, R2, RE, RC, B, VBE, VCEsat):
    Rth = R1*R2/(R1+R2)
    vth = vcc*R2/(R1+R2)
    IB = (vth-VBE)/(Rth+(1+B)*RE)
    ICsat = (vcc-VCEsat) /(RC+RE)
    IC = B*IB
 if ((IC < ICsat) and (IC > 0)):
        IC = B*IB
        VCE = vcc-IC*(RE+RC)
        print('The transistor is in the Active mode')
    elif( IC>=ICsat):
        IC = ICsat
        VCE = VCEsat
        print('The transistor is in the Saturation mode')
    else:
        IB = 0
        IC = 0
        VCE = vcc
        print('The transistor is in the cut-off mode')
    Pc = IC*VCE
    return (IB, IC, VCE, Pc)
    # Define the circuit parameters
vcc = 20  # V
RB1 = 100  # kOhm
RB2 = 10  # kOhm
RC = 3   # kOhm
RE = 1   # kOhm
B = 100
VBE = 0.7  # V
VCEsat = 0  # V

# Create a figure and axis
fig = plt.figure(figsize=(12,6))
ax = fig.add_subplot(121, projection='3d')
plt.subplots_adjust(bottom=0.25, right=0.75)  # make room for sliders

# Create sliders for interactive input
vcc_slider_ax = plt.axes([0.25, 0.15, 0.65, 0.03])
RB1_slider_ax = plt.axes([0.25, 0.1, 0.65, 0.03])
RB2_slider_ax = plt.axes([0.25, 0.05, 0.65, 0.03])

vcc_slider = Slider(vcc_slider_ax, 'Vcc', 10, 30, valinit=vcc, color='orange')
RB1_slider = Slider(RB1_slider_ax, 'RB1', 50, 200, valinit=RB1, color='green')
RB2_slider = Slider(RB2_slider_ax, 'RB2', 5, 20, valinit=RB2, color='blue')

# Define an update function for the sliders
def update(val):
    vcc = vcc_slider.val
    RB1 = RB1_slider.val
    RB2 = RB2_slider.val
    IB, IC, VCE, Pc = VoltageDividerBJT(vcc, RB1, RB2, RE, RC, B, VBE, VCEsat)
    ax.clear()
    ax.set_xlabel('Voltage (V)', color='orange')
    ax.set_ylabel('Current (A)', color='orange')
    ax.set_zlabel('Power (W)', color='orange')
    ax.set_title('BJT Voltage Divider Circuit', color='orange')
    ax.plot([0, vcc], [0, 0], [0, 0], 'k-')  # ground
    ax.plot([vcc, vcc], [0, IC], [0, Pc], 'b-', label='Collector')  # collector
    ax.plot([vcc, 0], [IC, 0], [Pc, 0], 'r-', label='Emitter')  # emitter
    ax.plot([0, vcc], [VBE, VBE], [0, 0], 'g-', label='Base-Emitter Junction')  # base-emitter junction
    ax.plot([0, vcc], [IC, IC], [Pc, Pc], 'k--', label='Load Line')  # load line
    ax.legend()
    plt.draw_idle()

# Register the update function with the sliders
vcc_slider.on_changed(update)
RB1_slider.on_changed(update)
RB2_slider.on_changed(update)

# Create a 3D circuit diagram
ax_circuit = fig.add_subplot(122, projection='3d')
ax_circuit.set_axis_off()
ax_circuit.set_title('BJT Voltage Divider Circuit Diagram')
ax_circuit.text(0.1, 0.9, 0, 'Vcc', ha='center')
ax_circuit.text(0.1, 0.8, 0, 'RB1', ha='center')
ax_circuit.text(0.1, 0.7, 0, 'RB2', ha='center')
ax_circuit.text(0.1, 0.6, 0, 'RC', ha='center')
ax_circuit.text(0.1, 0.5, 0, 'RE', ha='center')
ax_circuit.text(0.1, 0.4, 0, 'B', ha='center')
ax_circuit.text(0.1, 0.3, 0, 'VBE', ha='center')
ax_circuit.text(0.1, 0.2, 0, 'VCEsat', ha='center')

# Initialize the plot
IB, IC, VCE, Pc = VoltageDividerBJT(vcc, RB1, RB2, RE, RC, B, VBE, VCEsat)
ax.plot([0, vcc], [0, 0], [0, 0], 'k-')  # ground
ax.plot([vcc, vcc], [0, IC], [0, Pc], 'b-', label='Collector')  # collector
ax.plot([vcc, 0], [IC, 0], [Pc, 0], 'r-', label='Emitter')  # emitter
ax.plot([0, vcc], [VBE, VBE], [0, 0], 'g-', label='Base-Emitter Junction')  # base-emitter junction
ax.plot([0, vcc], [IC, IC], [Pc, Pc], 'k--', label='Load Line')  # load line
ax.legend()

plt.show()
