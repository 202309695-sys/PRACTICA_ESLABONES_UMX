% =========================================================================
% BRAZO ROBÓTICO 2D - 3 GDL (ANIMACIÓN Y ECUACIÓN AL FINAL)
% =========================================================================
clc; clear;

% 1. EL USUARIO INGRESA LAS LONGITUDES
disp('=== CONFIGURACIÓN DE LOS ESLABONES ===');
l1_num = input('Ingrese la medida del eslabón 1: ');
l2_num = input('Ingrese la medida del eslabón 2: ');
l3_num = input('Ingrese la medida del eslabón 3: ');

% 2. EL USUARIO INGRESA LOS ÁNGULOS OBJETIVO (En grados)
disp(' ');
disp('=== CONFIGURACIÓN DE LOS ÁNGULOS FINALES ===');
th1_deg = input('Ingrese el ángulo final del eslabón 1 (en grados): ');
th2_deg = input('Ingrese el ángulo final del eslabón 2 (en grados): ');
th3_deg = input('Ingrese el ángulo final del eslabón 3 (en grados): ');

% Convertir los ángulos finales a radianes
th1_end = th1_deg * (pi/180);
th2_end = th2_deg * (pi/180);
th3_end = th3_deg * (pi/180);

% 3. PREPARAR LA ANIMACIÓN
fprintf('\nGenerando animación...\n');
frames = 100; % Cantidad de pasos para que el movimiento sea fluido

% Crear vectores que van desde 0 hasta el ángulo deseado
t1_traj = linspace(0, th1_end, frames);
t2_traj = linspace(0, th2_end, frames);
t3_traj = linspace(0, th3_end, frames);

% Configurar la ventana de la figura
figure('Name', 'Animación del Brazo al Objetivo', 'Color', 'w');
max_len = l1_num + l2_num + l3_num; % Para mantener el marco de la gráfica fijo

% 4. BUCLE DE ANIMACIÓN
for i = 1:frames
    % Ángulos en el instante actual
    th1 = t1_traj(i);
    th2 = t2_traj(i);
    th3 = t3_traj(i);

    % Matrices de rotación numéricas para el paso actual
    R1 = [cos(th1), -sin(th1); sin(th1), cos(th1)];
    R2 = [cos(th2), -sin(th2); sin(th2), cos(th2)];
    R3 = [cos(th3), -sin(th3); sin(th3), cos(th3)];

    % Posiciones de cada articulación
    p0 = [0; 0];
    p1 = R1 * [l1_num; 0];
    p2 = p1 + R1 * R2 * [l2_num; 0];
    p3 = p2 + R1 * R2 * R3 * [l3_num; 0];

    % Extraer coordenadas X y Y
    X_coords = [p0(1), p1(1), p2(1), p3(1)];
    Y_coords = [p0(2), p1(2), p2(2), p3(2)];

    % Dibujar el brazo
    plot(X_coords, Y_coords, '-o', 'LineWidth', 4, 'MarkerSize', 8, ...
        'MarkerFaceColor', 'r', 'Color', '#0072BD');

    % Mantener la gráfica estática para que solo se mueva el brazo
    axis([-max_len-1, max_len+1, -max_len-1, max_len+1]);
    grid on;

    % Títulos y etiquetas que se actualizan
    title(sprintf('Moviendo hacia: [%d°, %d°, %d°]', th1_deg, th2_deg, th3_deg));
    xlabel('X'); ylabel('Y');

    % Forzar la actualización en pantalla
    drawnow;

    % Pequeña pausa para controlar la velocidad
    pause(0.02);
end

% 5. CÁLCULO Y MUESTRA DE ECUACIONES DEL MOVIMIENTO AL FINALIZAR
syms theta1 theta2 theta3 L1 L2 L3 real

% Matrices de Rotación Simbólicas
R1_sym = [cos(theta1), -sin(theta1); sin(theta1), cos(theta1)];
R2_sym = [cos(theta2), -sin(theta2); sin(theta2), cos(theta2)];
R3_sym = [cos(theta3), -sin(theta3); sin(theta3), cos(theta3)];

% Cinemática Simbólica
P1_sym = R1_sym * [L1; 0];
P2_sym = P1_sym + R1_sym * R2_sym * [L2; 0];
P3_sym = P2_sym + R1_sym * R2_sym * R3_sym * [L3; 0];

disp(' ');
disp('=== ANIMACIÓN FINALIZADA ===');
disp('=== ECUACIÓN QUE DESCRIBE EL MOVIMIENTO DEL EFECTOR FINAL ===');
disp('Posición en el Eje X:'); disp(simplify(P3_sym(1)));
disp('Posición en el Eje Y:'); disp(simplify(P3_sym(2)));
disp('===============================================================');
