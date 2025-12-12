---
layout: project
title: MAE 3270 Final Project
description: Torque Wrench Design
technologies: [Autodesk Fusion 360 and Ansys]
image: assets/images/FEM.png
---

Reflection

1. Image(s) of CAD model. Must show all key dimensions.

![CAD screenshot]({{ "/assets/images/screenshot-2025-12-10-8-28-34-PM.png" | relative_url }})
![CAD screenshot 2]({{ "/assets/images/Cad 2.png" | relative_url }})

2. Describe material used and its relevant mechanical properties.

Material: Aluminum 7075 T62 
This material is ultimately successful due to its significantly lowered Young’s Modulus (approx 10x10^6 psi). This means that the material is less stiff than many other titanium or steel materials, and ultimately led to the torque wrench’s strain gauge’s voltage output being adequate for the constraints of the design. The lowered stiffness allowed for the dimensions of the wrench (b and h) to be large enough to meet the stress and fatigue factor of safety requirements. 

3. Diagram communicating how loads and boundary conditions were applied to your FEM

Model. (screenshot)
![Boundary conditions]({{ "/assets/images/BCs.png" | relative_url }})

4. Normal strain contours (in the strain gauge direction) from FEM

![Strain contours]({{ "/assets/images/Strain.png" | relative_url }})

5. Contour plot of maximum principal stress from FEM

![Max principal stress]({{ "/assets/images/Max Stress.png" | relative_url }})

6. Summarize results from FEM calculation showing maximum normal stress (anywhere),
load point deflection, strains at the strain gauge locations

Max Normal Stress: 57064 psi (at stress singularity) 

Max Deflection: 0.43619 in

Strain at Gauge: 1.2002 x 10^-3 strains (1200.2 microstrains) 

Note: This almost exactly matches matlab calculation output

7. Torque wrench sensitivity in mV/V using strains from the FEM analysis

Strain measured at Gauge: 1.2002 x 10^-3 strains (1200.2 microstrains)

1 strain * 1000 mV/V/ strain (for a half-bridge strain gauge)

Output voltage from FEM strain: 1.2 V

→ This meets voltage requirements for the torque wrench


8. Strain gauge selected (give type and dimensions). Note that design must physically
have enough space to bond the gauges

Strain Gauge Type: Half-bridge strain gauge 

Dimensions: 1 mm x 3.75 mm (0.039 in x 0.148 in)

This fits the torque wrench’s side





Details on my design:

Material: Aluminum 7075

Dimensions:  

L = 16 in

h = 0.75 in

b = 0.5 in 

c = 1 in

Matlab Output (script appended):

![Matlab output]({{ "/assets/images/Matlab Output.png" | relative_url }})


Matlab Script for hand calcs:
%% Variables
% values specified in problem statement
M = 600; % max torque (in-lbf), required parameter
a = 0.04; %specified crack dimensions
ncycles = 10e6; %specified number of cycles
% dimensions, change these (make a func where these are variables??
L = 16; %Length from drive to where load is applied (in)
h = 0.75; % width (in)
b = 0.5; %thickness (in)
c = 1; %distance from center of drive to center of strain gauge (in)
% Material Properties, change these based on material choice 
%Original M42 Steel (brittle with low toughness)
% E = 32.E6; %Young's Modulus (psi), based on material 
% nu = 0.29; % poissons ratio
% su = 370.E3; %tensile strength, yield or ultimate strength (psi)
% KIC = 15.E3; %fracture toughness (psi sqrt(in))
% sfatigue = 115.e3; %fatigue strength from Granta 10^6 cycles, psi
% name = 'M42 Steel'; %material name
%Chosen Material: Titanium near-alpha-alloy
E = 10.E6; %Young's Modulus (psi), based on material 
nu = 0.33; % poissons ratio
su = 80.E3; %tensile strength, yield or ultimate strength (psi)
KIC = 35.E3 ; %fracture toughness (psi sqrt(in))
sfatigue = 35.E3; %fatigue strength from Granta 10^6 cycles, psi
% name = 'Aluminum 7075'

%Calculations
%stress and strain analysis
F = M/L; %applied force 
I = 1/12*b*h^3; %moment of inertia
nstress = M*h/2/I%bending stress due to moment
gauge_moment = M/L*(L-c); %moment at gauge
gauge_stress = gauge_moment*h/2/I;
eps = gauge_stress/ E * 10^6 %strain value, microstrains
voltage = eps / 1000  %output voltage (mV/V), must be at least 1mV/V (full bridge)
umax = (F*(L^3))/(3*E*I) %deflection, in 

%see if voltage is met
if voltage < 1
    fprintf('Not enough voltage')
end


%fracture analysis 
Kic_calc = 1.12*(nstress)*sqrt(pi*a); %Kic, since a/b small, can use approx

%Factors of safety
Xo = su/nstress; %factor of safety for strength from bending, must be > 4
Xk = KIC/ Kic_calc; %factor of safety for crack growth
Xs = sfatigue/ nstress; %factor of safety for fatigue, gives us values so dont need analysis

% see if FOS even work
if Xo < 4
    fprintf('Xo too small')
end

if Xk < 2
    fprintf('Xk too small')
end

if Xs < 1.5
    fprintf('Xs too small')
end


% Display the factors of safety
fprintf('Factor of Safety for Strength (Xo): %.2f\n', Xo);
fprintf('Factor of Safety for Crack Growth (Xk): %.2f\n', Xk);
fprintf('Factor of Safety for Fatigue (Xs): %.2f\n', Xs);
