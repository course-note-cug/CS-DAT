# PA2

新版的指令非常好懂得. 并且已经做好了signextension. 

## ELF
```
riscv64-linux-gnu-readelf -a add-riscv32-nemu.elf
``` 

用这个来读ELF

Name属性之后, 你会发现这些表项正好对应了程序中定义的函数, 相应的Value属性正好是它们的起始地址, 而相应的Size属性则给出了函数的大小.

```cpp
#include <stdio.h>
#include <elf.h>

void print_symbol_table(Elf32_Sym *symbol_table, int symbol_count, char *string_table) {
    printf("Symbol Table:\n");
    printf("  Num:    Value     Size     Type    Bind    Name\n");

    for (int i = 0; i < symbol_count; ++i) {
        Elf32_Sym *symbol = &symbol_table[i];

        // Get the symbol name from the string table
        char *symbol_name = string_table + symbol->st_name;

        printf("  %-6d:  %08x  %-8u ", i, symbol->st_value, symbol->st_size);

        switch (ELF32_ST_TYPE(symbol->st_info)) {
            case STT_NOTYPE:
                printf("NOTYPE  ");
                break;
            case STT_OBJECT:
                printf("OBJECT  ");
                break;
            case STT_FUNC:
                printf("FUNC    ");
                break;
            case STT_SECTION:
                printf("SECTION ");
                break;
            case STT_FILE:
                printf("FILE    ");
                break;
            default:
                printf("UNKNOWN ");
                break;
        }

        switch (ELF32_ST_BIND(symbol->st_info)) {
            case STB_LOCAL:
                printf("LOCAL ");
                break;
            case STB_GLOBAL:
                printf("GLOBAL");
                break;
            case STB_WEAK:
                printf("WEAK  ");
                break;
            default:
                printf("UNKWN ");
                break;
        }

        printf(" %s\n", symbol_name);
    }
}

int main() {
    FILE *file;
    Elf32_Ehdr elf_header;

    file = fopen("your_elf_file", "rb");
    if (file == NULL) {
        perror("Error opening file");
        return 1;
    }

    fread(&elf_header, sizeof(Elf32_Ehdr), 1, file);

    // Get the section header table offset
    fseek(file, elf_header.e_shoff, SEEK_SET);

    // Read the section header table
    Elf32_Shdr *section_headers = malloc(elf_header.e_shentsize * elf_header.e_shnum);
    fread(section_headers, elf_header.e_shentsize, elf_header.e_shnum, file);

    // Find the symbol table section and the associated string table
    int symbol_table_index = -1;
    int string_table_index = -1;

    for (int i = 0; i < elf_header.e_shnum; ++i) {
        if (section_headers[i].sh_type == SHT_SYMTAB) {
            symbol_table_index = i;
            string_table_index = section_headers[i].sh_link;
            break;
        }
    }

    if (symbol_table_index == -1 || string_table_index == -1) {
        printf("Symbol table or string table not found\n");
        return 1;
    }

    // Calculate the number of symbol table entries
    int symbol_count = section_headers[symbol_table_index].sh_size / sizeof(Elf32_Sym);

    // Get the symbol table offset
    Elf32_Sym *symbol_table = malloc(section_headers[symbol_table_index].sh_size);
    fseek(file, section_headers[symbol_table_index].sh_offset, SEEK_SET);
    fread(symbol_table, sizeof(Elf32_Sym), symbol_count, file);

    // Get the string table offset and size
    Elf32_Off string_table_offset = section_headers[string_table_index].sh_offset;
    Elf32_Word string_table_size = section_headers[string_table_index].sh_size;

    // Allocate memory for the string table
    char *string_table = malloc(string_table_size);
    fseek(file, string_table_offset, SEEK_SET);
    fread(string_table, string_table_size, 1, file);

    // Print the symbol table entries
    print_symbol_table(symbol_table, symbol_count, string_table);

    // Clean up
    free(section_headers);
    free(symbol_table);
    free(string_table);
    fclose(file);

    return 0;
}
```