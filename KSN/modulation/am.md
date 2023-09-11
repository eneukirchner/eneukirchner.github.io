---
sort: 1
---
# Amplitudenmodulation

```code
%sample rate
fs = 10000;
Ts = 1/fs;
t=0:Ts:1-Ts; % Time vector start/step/stop

% Generate wave
fc=1000; 
uc=cos(2*pi*fc*t); 

fb1=200;
m1=0.8;
ub1=cos(2*pi*fb1*t);

fb2=500;
m2=0.4;
ub2=cos(2*pi*fb2*t);

uam=uc.*(1+m1*ub1+m2*ub2);
% save('am.mat', 'uam');

% Plot Time Domain
figure(1)
subplot(2,1,1);  % 2 rows, 1 column, 1st row
plot(t(1:500),uam(1:500)), grid on;
title("Time Domain");
xlabel('time');
ylabel('voltage');

% Calculate Spectrum 
F = fft(uam);
n = length(uam);          % number of samples
f = (0:n-1)*(fs/n);     % frequency range
volt = 2/n*abs(F);    % voltage of the DFT
power = volt.^2;

% Plot Frequency Domain
subplot(2,1,2);  % 3 rows, 1 column, 1st row
plot(f,volt);
grid on;
title("Frequency Domain");
xlabel('frequency')
ylabel('power');
```

