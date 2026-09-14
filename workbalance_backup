CREATE TABLE roles (
    id_rol SERIAL PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL UNIQUE,
    descripcion TEXT
);

CREATE TABLE departamentos (
    id_departamento SERIAL PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL UNIQUE,
    descripcion TEXT,
    activo BOOLEAN NOT NULL DEFAULT TRUE
);

CREATE TABLE usuarios (
    id_usuario SERIAL PRIMARY KEY,
    cedula VARCHAR(20) NOT NULL UNIQUE,
    nombre_completo VARCHAR(150) NOT NULL,
    fecha_nacimiento DATE,
    correo VARCHAR(150) NOT NULL UNIQUE,
    celular VARCHAR(20),
    cargo VARCHAR(100),
    direccion VARCHAR(200),
    contacto_emergencia VARCHAR(150),
    genero VARCHAR(30),
    password VARCHAR(255) NOT NULL,
    id_rol INTEGER NOT NULL,
    id_departamento INTEGER NOT NULL,
    fecha_creacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    activo BOOLEAN NOT NULL DEFAULT TRUE,

    CONSTRAINT fk_usuario_rol
        FOREIGN KEY (id_rol)
        REFERENCES roles(id_rol)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,

    CONSTRAINT fk_usuario_departamento
        FOREIGN KEY (id_departamento)
        REFERENCES departamentos(id_departamento)
        ON UPDATE CASCADE
        ON DELETE RESTRICT
);

CREATE TABLE tareas (
    id_tarea SERIAL PRIMARY KEY,
    titulo VARCHAR(200) NOT NULL,
    descripcion TEXT,
    medio_recepcion VARCHAR(50),
    prioridad VARCHAR(20) NOT NULL,
    estado VARCHAR(30) NOT NULL DEFAULT 'Pendiente',
    fecha_creacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    fecha_inicio TIMESTAMP,
    fecha_limite TIMESTAMP NOT NULL,
    tiempo_estimado NUMERIC(6,2) NOT NULL,
    notas TEXT,
    id_creador INTEGER NOT NULL,

    CONSTRAINT fk_tarea_creador
        FOREIGN KEY (id_creador)
        REFERENCES usuarios(id_usuario)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,

    CONSTRAINT chk_prioridad
        CHECK (prioridad IN ('Crítica', 'Alta', 'Media', 'Baja')),

    CONSTRAINT chk_estado
        CHECK (estado IN ('Pendiente', 'En progreso', 'Completada', 'Bloqueada')),

    CONSTRAINT chk_tiempo_estimado
        CHECK (tiempo_estimado > 0),

    CONSTRAINT chk_fechas_tarea
        CHECK (
            fecha_inicio IS NULL
            OR fecha_inicio <= fecha_limite
        )
);

CREATE TABLE tareas_responsables (
    id_tarea_responsable SERIAL PRIMARY KEY,
    id_tarea INTEGER NOT NULL,
    id_usuario INTEGER NOT NULL,
    fecha_asignacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_responsable_tarea
        FOREIGN KEY (id_tarea)
        REFERENCES tareas(id_tarea)
        ON UPDATE CASCADE
        ON DELETE CASCADE,

    CONSTRAINT fk_responsable_usuario
        FOREIGN KEY (id_usuario)
        REFERENCES usuarios(id_usuario)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,

    CONSTRAINT uq_tarea_usuario
        UNIQUE (id_tarea, id_usuario)
);


CREATE TABLE subtareas (
    id_subtarea SERIAL PRIMARY KEY,
    id_tarea INTEGER NOT NULL,
    titulo VARCHAR(200) NOT NULL,
    descripcion TEXT,
    estado VARCHAR(30) NOT NULL DEFAULT 'Pendiente',
    fecha_creacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    fecha_limite TIMESTAMP,

    CONSTRAINT fk_subtarea_tarea
        FOREIGN KEY (id_tarea)
        REFERENCES tareas(id_tarea)
        ON UPDATE CASCADE
        ON DELETE CASCADE,

    CONSTRAINT chk_subtarea_estado
        CHECK (estado IN ('Pendiente', 'En progreso', 'Completada', 'Bloqueada')),

    CONSTRAINT chk_subtarea_fecha
        CHECK (
            fecha_limite IS NULL
            OR fecha_limite >= fecha_creacion
        )
);


CREATE TABLE historial_tareas (
    id_historial SERIAL PRIMARY KEY,
    id_tarea INTEGER NOT NULL,
    id_usuario INTEGER NOT NULL,
    campo_modificado VARCHAR(100) NOT NULL,
    valor_anterior TEXT,
    valor_nuevo TEXT,
    fecha_cambio TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    comentario TEXT,

    CONSTRAINT fk_historial_tarea
        FOREIGN KEY (id_tarea)
        REFERENCES tareas(id_tarea)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,

    CONSTRAINT fk_historial_usuario
        FOREIGN KEY (id_usuario)
        REFERENCES usuarios(id_usuario)
        ON UPDATE CASCADE
        ON DELETE RESTRICT
);

CREATE TABLE comentarios (
    id_comentario SERIAL PRIMARY KEY,
    id_tarea INTEGER NOT NULL,
    id_usuario INTEGER NOT NULL,
    contenido TEXT NOT NULL,
    fecha_creacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,

    CONSTRAINT fk_comentario_tarea
        FOREIGN KEY (id_tarea)
        REFERENCES tareas(id_tarea)
        ON UPDATE CASCADE
        ON DELETE CASCADE,

    CONSTRAINT fk_comentario_usuario
        FOREIGN KEY (id_usuario)
        REFERENCES usuarios(id_usuario)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,

    CONSTRAINT chk_comentario_contenido
        CHECK (LENGTH(TRIM(contenido)) > 0)
);

CREATE TABLE cargas_laborales (
    id_carga SERIAL PRIMARY KEY,
    id_usuario INTEGER NOT NULL,
    fecha DATE NOT NULL,
    horas_disponibles NUMERIC(6,2) NOT NULL,
    horas_asignadas NUMERIC(6,2) NOT NULL,
    porcentaje_carga NUMERIC(6,2) NOT NULL,
    nivel_carga VARCHAR(20) NOT NULL,

    CONSTRAINT fk_carga_usuario
        FOREIGN KEY (id_usuario)
        REFERENCES usuarios(id_usuario)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,

    CONSTRAINT chk_horas_disponibles
        CHECK (horas_disponibles >= 0),

    CONSTRAINT chk_horas_asignadas
        CHECK (horas_asignadas >= 0),

    CONSTRAINT chk_porcentaje_carga
        CHECK (porcentaje_carga >= 0),

    CONSTRAINT chk_nivel_carga
        CHECK (nivel_carga IN ('Normal', 'Alta', 'Sobrecarga')),

    CONSTRAINT uq_usuario_fecha_carga
        UNIQUE (id_usuario, fecha)
);


CREATE TABLE alertas (
    id_alerta SERIAL PRIMARY KEY,
    id_usuario INTEGER NOT NULL,
    id_tarea INTEGER,
    tipo VARCHAR(50) NOT NULL,
    descripcion TEXT NOT NULL,
    fecha_generacion TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    estado VARCHAR(20) NOT NULL DEFAULT 'Activa',
    fecha_atencion TIMESTAMP,

    CONSTRAINT fk_alerta_usuario
        FOREIGN KEY (id_usuario)
        REFERENCES usuarios(id_usuario)
        ON UPDATE CASCADE
        ON DELETE RESTRICT,

    CONSTRAINT fk_alerta_tarea
        FOREIGN KEY (id_tarea)
        REFERENCES tareas(id_tarea)
        ON UPDATE CASCADE
        ON DELETE SET NULL,

    CONSTRAINT chk_tipo_alerta
        CHECK (
            tipo IN (
                'Tarea vencida',
                'Tarea próxima a vencer',
                'Sobrecarga laboral'
            )
        ),

    CONSTRAINT chk_estado_alerta
        CHECK (
            estado IN ('Activa', 'Atendida', 'Descartada')
        ),

    CONSTRAINT chk_fecha_atencion
        CHECK (
            fecha_atencion IS NULL
            OR fecha_atencion >= fecha_generacion
        )
);

INSERT INTO roles (nombre, descripcion)
VALUES
('Administrador', 'Gestiona usuarios, roles y configuraciones generales'),
('Supervisor', 'Asigna tareas y supervisa la carga laboral'),
('Empleado', 'Consulta y gestiona sus tareas asignadas');


INSERT INTO departamentos (nombre, descripcion)
VALUES
('Tecnología', 'Área de tecnología y desarrollo'),
('Recursos Humanos', 'Área de gestión humana'),
('Administración', 'Área administrativa'),
('Finanzas', 'Área financiera');

