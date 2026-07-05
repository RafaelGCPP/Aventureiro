CC := cc
CFLAGS := -std=c11 -Wall -Wextra -Werror -g
LDLIBS :=
SRC_DIR := src
BUILD_DIR := build
BIN := $(BUILD_DIR)/aventureiro

# Pacote 0: ainda não há .c de jogo, só o cJSON vendorizado.
# Este alvo existe para provar que a toolchain compila algo do projeto.
SRCS := $(SRC_DIR)/cJSON.c
OBJS := $(SRCS:$(SRC_DIR)/%.c=$(BUILD_DIR)/%.o)

.PHONY: all clean test

all: $(BUILD_DIR)/cJSON.o

$(BUILD_DIR)/%.o: $(SRC_DIR)/%.c | $(BUILD_DIR)
	$(CC) $(CFLAGS) -c $< -o $@

$(BUILD_DIR):
	mkdir -p $(BUILD_DIR)

clean:
	rm -rf $(BUILD_DIR)

test:
	@echo "Nenhum teste ainda (chega no Pacote 9)."
