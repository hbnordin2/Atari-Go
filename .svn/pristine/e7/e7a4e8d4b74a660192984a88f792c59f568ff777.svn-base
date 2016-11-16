#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <string.h>
#include <errno.h>
#include <unistd.h>

#define HUMAN 0
#define COMPUTER 1
#define O_STONE 2
#define X_STONE 3
#define LIBERTY 0

/* The board in play */
typedef struct {
    int width; /* The width of the board */
    int height; /* The height of the board */
    int widthLastIndex; /* The last index of width on the board */
    int heightLastIndex; /* The last index of height on the board */
    int** board; /* The board to be populated with stones */
} Board;

/* A player in the game */
typedef struct {
    int type; /* The type of player */
    int rowToMove; /* The next row a computer version will try for */
    int columnToMove; /* The next column a computer version will try for */
    int initialRow; /* The initial row a computer version will try for */
    int initialColumn; /* The initial column a computer version will try for */
    int multiplicationFactor; /* The multiplication factor */
    int spacer; /* A calculation to spread out the moves calculated */
    int instantiatedRow; /* The current row the computer will try for */
    int instantiatedColumn; /* The current column the computer will try for */
    int moveCounter; /* The number of moves pulled out of the AI sequence */
    int gridWidth; /* The width of the grid */
    int gridHeight; /* The height of the grid */
} Player;

/* The game in play */
typedef struct {
    int currentPlayer; /* The current player to move */
    Board* board; /* The board the game will be played on */
    Player** players; /* The players in the game */
    bool oLost; /* True if and only if player O has a captured string */
    bool xLost; /* True if and only if player X has a captured string */
} Game;

FILE* open_load_file(char*);
bool check_if_numbers(char**);
void change_current_player(Game*); 
void check_invocation(int, char**);
void correct_arguments_number(int);
void correct_player_types(char**);
void correct_board_range(char**);
Board* create_board(char**);
void clean_board(Board*);
void display_board(Board*);
Game* create_new_game(char**);
Player** create_players(char**);
void run_game(Game*);
void display_turn(Game*);
void make_player_move(Game*);
void initialize_ai_moves(Game*);
void make_move(Game*);
void place_stone(int, int, int, Game*);
void generate_ai_move(Game*);
bool position_unoccupied(Game*);
void check_for_captures(Game*);
bool check_string_completion(Game*, int, int);
void put_stones_back(Game*, int);
bool liberties_present(Game*, int, int);
char** split_string(char*, int);
int* convert_str_to_int(char**, int);
bool check_normal_input(char*);
void flush_input();
char* get_raw_input(int);
bool is_input_size_correct(char*, int);
bool check_valid_range(int, int, int);
bool is_unoccupied(int*, Game*);
char* read_in_file_content(FILE*);
int get_file_size(FILE*);
char* get_file_content(FILE*, int);
bool is_user_move_structurally_correct(char*);
void make_ai_move(Game*);
bool is_user_move_in_board(int*, Board*);
int* get_valid_player_move(Game*);
int count_this_char(char*, char);
int count_the_words(char*);
bool check_if_only_numbers(char*);
char* get_line(char*, int);
int get_line_index(char*, int);
int get_line_size(char*, int);
char* get_line_content(char*, int, int);
Game* load_saved_game(char**);
void check_minimum_number_of_lines(char*, int);
int get_number_of_lines(char*);
void check_first_line(char*);
char* get_first_line(char*);
bool check_dimensions(int*);
bool check_next_player(int*);
bool check_valid_next_tries(int*);
Game* load_game(char*);
Player** load_players(int*);
Board* load_board(int, int);
void assign_player_types(Game*, char*, char*);
int get_grid_size(char*, int);
char* get_grid(char*);
void check_grid(char*, Game*);
void check_rows(char*, int);
void check_width(char*, int);
void check_grid_content(char*);
void copy_grid(char*, Game*);
int save_game(Game*, char*);
bool check_location(char*);
void write_to_save_file(Game*, char*);
char* get_file_path(char*);
void write_header(FILE*, Game*);
void write_board(FILE*, Game*);
void write_computer_move(FILE*, Game*);
void crown_winner(Game*);
Game* set_up_game(int, char**);
void error_handle(int);

int main(int argc, char** argv) {
    check_invocation(argc, argv);
    Game* game = set_up_game(argc, argv);
    run_game(game);
}

/**
 * @brief Creates a game depending on whether an invocation is for a load game
 * or a create game.
 *
 * @param argumentCount
 *      The number of arguments passed in at invocation
 *
 * @param argumentContent
 *      The arguments passed in at invocation
 */
Game* set_up_game(int argumentCount, char** argumentContent) {
    Game* game;
    if(argumentCount == 4) {
        game = load_saved_game(argumentContent);
    } else {
        game = create_new_game(argumentContent);
    }
    return game;
}

/**
 * @brief Terminates the program if invocation is not valid
 *
 * @param argumentCount
 *      The number of arguments in the user input
 *
 * @param argumentContent
 *      The list of arguements provided by the user
 */
void check_invocation(int argumentCount, char** argumentContent) {
    correct_arguments_number(argumentCount);
    correct_player_types(argumentContent);
    if(argumentCount == 5) {
        correct_board_range(argumentContent);
    }
}

/** 
 * @brief Terminates the program if and only if argumentCount is 4 or 5
 *
 * @param argumentCount
 *      The number of command line arguments provided to the program
 */
void correct_arguments_number(int argumentCount) {
    if(argumentCount != 4 && argumentCount != 5) {
        error_handle(1);
    }
}

/**
 * @brief Terminates the program if and only if player types provided at 
 * program invocation are invalid
 *
 * @param arguments
 *      The list of arguments provided to the commandline
 */
void correct_player_types(char** arguments) {
    int isComputer;
    int isHuman;
    for(int i = 1; i <= 2; i++) {
        isComputer = strcmp(arguments[i], "c");
        isHuman = strcmp(arguments[i], "h");
        if(isComputer != 0 && isHuman != 0) {
            error_handle(2);
        }
    }
}

/**
 * @brief Checks that the values entered by the user represent valid integers
 * in the range (inclusive) 4 to 1000. If not, program will terminate.
 *
 * @param arguments
 *      The arguments provided at the commandline
 * */
void correct_board_range(char** arguments) {
    if(!check_if_numbers(arguments)) {
        error_handle(3);
    }
    errno = 0;
    int height = strtol(arguments[3], NULL, 10);
    int width = strtol(arguments[4], NULL, 10);
    if(errno != 0) {
        error_handle(3);
    }
    if(height > 1000 || width > 1000 || height < 4 || width < 4) {
        error_handle(3);
    }
}

/**
 * @brief Checks if values entered as board dimensions are characters that 
 * correspond to integers
 *
 * @param arguments
 *      The list of arguments provided by the user from the command line
 *
 * @return true if all values corresponding to board dimensions comprise only
 * of ASCII numbers, false otherwise
 */
bool check_if_numbers(char** arguments) {
    for(int i = 3; i <= 4; i++) {
        int j = 0;
        char start = arguments[i][j];
        while(start != '\0') {
            if(start > 57 || start < 48) {
                return false;
            }
            j++;
            start = arguments[i][j];
        }
    }
    return true;
}

/**
 * @brief Loads a game as specified by the command line arguments. Terminates
 * if the load file is incorrect.
 *
 * @param arguments
 *      The command-line arguments
 *
 * @return The game as specified by the command line arguments and file
 * specified by the command line arguments.
 */
Game* load_saved_game(char** arguments) {
    char* fileContent;
    FILE* file = open_load_file(arguments[3]);
    fileContent = read_in_file_content(file);
    check_minimum_number_of_lines(fileContent, 5);
    check_first_line(fileContent);
    Game* game = load_game(fileContent);
    assign_player_types(game, arguments[1], arguments[2]);
    check_grid(fileContent, game);
    copy_grid(fileContent, game);
    free(fileContent);
    return game;
}

/**
 * @brief Returns a file if fileName is the name of a file that exists,
 * otherwise it terminates the program.
 *
 * @param fileName
 *      The name of the file to load in
 *
 * @return The file named fileName
 */
FILE* open_load_file(char* fileName) {
    FILE* file = fopen(fileName, "r");
    if(!file) {
        error_handle(4);
    }
    return file;
}

/**
 * @brief Copies the content from a grid in fileContent into the board in a
 * game
 *
 * @param game
 *      The game whose board the content will be copied to
 *
 * @param fileContent
 *      The contents of a file read in, which contains a board
 */
void copy_grid(char* fileContent, Game* game) {
    char* grid;
    int i = 0;
    int j = 0;
    int index = 0;
    char valueAtIndex = 'a';
    grid = get_grid(fileContent);
    while(valueAtIndex != '\0') {
        valueAtIndex = grid[index];
        if(valueAtIndex == '\n') {
            j = 0;
            i += 1;
            index += 1;
            continue;
        }
        if(valueAtIndex == 'O') {
            game->board->board[i][j] = O_STONE;
        }
        if(valueAtIndex == 'X') {
            game->board->board[i][j] = X_STONE;
        }
        j += 1;
        index += 1;
    }
}

/**
 * @brief Assigns player types to players according to the load file
 *
 * @param game
 *      The game the players are in
 *
 * @param playerO
 *      The type of the first player
 *
 * @param playerX
 *      The type of the second player
 */
void assign_player_types(Game* game, char* playerO, char* playerX) {
    if(playerO[0] == 'h') {
        game->players[0]->type = HUMAN;
    } else {
        game->players[0]->type = COMPUTER;
    }
    if(playerX[0] == 'h') {
        game->players[1]->type = HUMAN;
    } else {
        game->players[1]->type = COMPUTER;
    }
}

/**
 * @brief Exits the program if a load file does not have the minimum 
 * number of lines
 *
 * @param fileContent
 *      The content of the file to be inspected and lines counted
 *
 * @param minimumLines
 *      The minimum number of lines the file should contain
 */
void check_minimum_number_of_lines(char* fileContent, int minimumLines) {
    int totalLines = 0;
    totalLines = get_number_of_lines(fileContent);
    if(totalLines < minimumLines) {
        error_handle(5);
    }
}

/**
 * @brief Gets the total number of lines in a file
 *
 * @param fileContent
 *      The content of the file whose lines will be counted
 *
 * @return The number of lines in the file
 */
int get_number_of_lines(char* fileContent) {
    char inspectValue = 'a';
    int index = 0;
    int totalLines = 1;
    while(inspectValue != '\0') {
        inspectValue = fileContent[index];
        if(inspectValue == '\n') {
            totalLines += 1;
        }
        index += 1;
    }
    return totalLines;
}

/**
 * @brief Checks the first line of a loaded file's contents, ensuring that
 * the values are correct
 *
 * @param fileContent
 *      The content of the file to be loaded
 */
void check_first_line(char* fileContent) {
    char* firstLine;
    bool isValid = true;
    firstLine = get_first_line(fileContent);
    isValid &= (count_this_char(firstLine, ' ') == 8);
    isValid &= (count_the_words(firstLine) == 9);
    isValid &= check_if_only_numbers(firstLine);
    if(!isValid) {
        error_handle(5);
    }
    char** loadArguments = split_string(firstLine, 9);
    int* parsedArguments = convert_str_to_int(loadArguments, 9);     
    isValid &= check_dimensions(parsedArguments);
    isValid &= check_next_player(parsedArguments);
    isValid &= check_valid_next_tries(parsedArguments);
    if(!isValid) {
        error_handle(5);
    }
}

/**
 * @brief Gets the first line in the content of a loaded game
 *
 * @param fileContent
 *      The content of a loaded game
 *
 * @return The first line in fileContent
 */
char* get_first_line(char* fileContent) {
    char* firstLine;
    firstLine = get_line(fileContent, 0);
    return firstLine;
}

/**
 * @brief Returns true if the dimensions specified in the loaded game are
 * correct
 *
 * @param arguments
 *      The arguments in the first line of a loaded game
 *
 * @return true if the dimensions specified in the loaded game are correct
 */
bool check_dimensions(int* arguments) {
    if(arguments[0] < 4 || arguments[1] < 4) {
        return false;
    }
    if(arguments[0] > 1000 || arguments[1] > 1000) {
        return false;
    }
    return true;
}

/**
 * @brief Returns true if the next player specified in the loaded game is
 * either 0 or 1
 *
 * @param arguments
 *      The arguments in the first line of a loaded game
 *
 * @return True if the next player is 0 or 1
 */
bool check_next_player(int* arguments) {
    if(arguments[2] != 0 && arguments[2] != 1) {
        return false;
    }
    return true;
}

/**
 * @brief Returns true if the next tries specified in the arguments of the
 * loaded game are within the confines of the board
 *
 * @param arguments
 *      The arguments specified in the loaded file
 *
 * @return True if and only if the next moves are within the confines of the
 * board
 */
bool check_valid_next_tries(int* arguments) {
    if(arguments[3] >= arguments[0]) {
        return false;
    }
    if(arguments[4] >= arguments[1]) {
        return false;
    }
    if(arguments[6] >= arguments[0]) {
        return false;
    }
    if(arguments[7] >= arguments[1]) {
        return false;
    }
    return true;
}

/**
 * @brief Returns true if a line consists only of whitespace and ASCII values
 * representing integers
 *
 * @param line
 *      The line to inspect
 * 
 * @return True if and only if the line consists of only whitespace and ASCII
 * values representing integers
 */
bool check_if_only_numbers(char* line) {
    int index = 0;
    char valueAtIndex = 'a';
    while(valueAtIndex != '\0') {
        valueAtIndex = line[index];
        if(valueAtIndex != ' ' && valueAtIndex != '\n' && valueAtIndex != '\0' 
                && !(valueAtIndex > 47 && valueAtIndex < 58)) {
            return false;
        }
        index += 1;
    }
    return true;
}

/**
 * @brief Returns the number of words in a line
 *
 * @param line
 *      The line whose words will be counted
 *
 * @return The number of words in line
 */
int count_the_words(char* line) {
    bool readyForNewWord = true;
    char valueAtIndex;
    int count = 0;
    for(int i = 0; i < strlen(line); i++) {
        valueAtIndex = line[i];
        if(readyForNewWord && valueAtIndex != ' ') {
            readyForNewWord = false;
            count += 1;
        }
        if(valueAtIndex == ' ') {
            readyForNewWord = true;
        }
    }
    return count;
}

/**
 * @brief Returns the number of occurances of a particular character in a line
 *
 * @param line
 *      The line containing characters
 *
 * @param target
 *      The character to be counted
 *
 * @return The number of characters in the line
 */
int count_this_char(char* line, char target) {
    int count = 0;
    int index = 0;
    char valueAtIndex = 'a';
    while(valueAtIndex != '\0') {
        valueAtIndex = line[index];
        if(valueAtIndex == target) {
            count += 1;
        }
        index += 1;
    }
    return count;
}

/**
 * @brief Returns the nth line in a block of text
 *
 * @param lines
 *      The block of text
 *
 * @param lineToGet
 *      The line to get in the block of text
 *
 * @return The line in lines numbered lineToGet
 */
char* get_line(char* lines, int lineToGet) {
    int index = get_line_index(lines, lineToGet);
    int size = get_line_size(lines, index);
    char* line = get_line_content(lines, index, size);
    return line;
}

/**
 * @brief Returns the index of the nth line in a block of text
 *
 * @param lines
 *      The block of text
 *
 * @param lineToGet
 *      The number of the line to get
 *
 * @return The starting index of the line in lines numbered lineToGet
 *
 */
int get_line_index(char* lines, int lineToGet) {
    int currentLine = 0;
    int index = 0;
    char valueAtIndex = 'a';
    while(currentLine < lineToGet) {
        valueAtIndex = lines[index];
        if(valueAtIndex == '\n') {
            currentLine += 1;
        }
        index += 1;
    }
    return index;
}

/**
 * @brief Returns the size of a line in a block of text
 *
 * @param lines
 *      The block of text
 *
 * @param lineLocation
 *      The index to start counting from
 *
 * @return The size of the line starting at index lineLocation
 */
int get_line_size(char* lines, int lineLocation) {
    int size = 1;
    int index = 0;
    char valueAtIndex = 'a';
    while(valueAtIndex != '\n') {
        valueAtIndex = lines[index + lineLocation];
        size += 1;
        index += 1;
    }
    return size;
}

/**
 * @brief Returns the content of a line in a block of text
 *
 * @param lines
 *      The block of text
 *
 * @param size
 *      The number of characters in the line
 *
 * @param lineLocation
 *      The starting index of the line to get
 *
 * @return The content of the line starting at lineLocation
 */
char* get_line_content(char* lines, int lineLocation, int size) {
    char* content = (char*) malloc(sizeof(char) * size);
    for(int i = 0; i < size - 1; i++) {
        content[i] = lines[lineLocation + i];
    }
    content[size - 1] = '\0';
    return content;
}

/**
 * @brief Returns a game as specified by a loaded game
 *
 * @param fileContent
 *      The content of the load game file
 *
 * @return The game specified by the load game file
 */
Game* load_game(char* fileContent) {
    char* firstLine = get_line(fileContent, 0);
    char** firstLineSplit = split_string(firstLine, 9);
    int* firstLineParsed = convert_str_to_int(firstLineSplit, 9);
    Player** players = load_players(firstLineParsed);
    Board* board = load_board(firstLineParsed[0], firstLineParsed[1]);
    Game* game = (Game*) malloc(sizeof(Game));
    game->board = board;
    game->players = players;
    game->oLost = false;
    game->xLost = false;
    if(firstLineParsed[2] == 1) {
        game->currentPlayer = 1;
    } else {
        game->currentPlayer = 0;
    }
    return game;
}

/**
 * @brief Loads in the attributes of the players as specified in the arguments
 * of a load game file
 *
 * @param arguments
 *      The arguments of a load game file
 *
 * @return A list of players with the attributes speicifed in the arguments
 * of a load game file
 */
Player** load_players(int* arguments) {
    Player** players = (Player**) malloc(sizeof(Player*) * 2);

    Player* playerO = (Player*) malloc(sizeof(Player));
    Player* playerX = (Player*) malloc(sizeof(Player)); 

    playerO->initialRow = 1; 
    playerO->initialColumn = 4;  
    playerO->multiplicationFactor = 29;
    playerO->gridWidth = arguments[1];
    playerO->gridHeight = arguments[0];
    playerO->moveCounter = arguments[5] + 1; 
    playerO->spacer = playerO->initialRow * playerO->gridWidth
            + playerO->initialColumn;
    playerO->rowToMove = arguments[3];
    playerO->columnToMove = arguments[4];
    playerO->instantiatedRow = arguments[3]; 
    playerO->instantiatedColumn = arguments[4]; 

    playerX->initialRow = 2;
    playerX->initialColumn = 10;
    playerX->multiplicationFactor = 17;
    playerX->gridWidth = arguments[1];
    playerX->gridHeight = arguments[0];
    playerX->moveCounter = arguments[8] + 1; 
    playerX->spacer = playerX->initialRow * playerX->gridWidth
            + playerX->initialColumn;
    playerX->rowToMove = arguments[6];
    playerX->columnToMove = arguments[7];
    playerX->instantiatedRow = arguments[6]; 
    playerX->instantiatedColumn = arguments[7]; 

    players[0] = playerO;
    players[1] = playerX;
    return players;
}

/**
 * @brief Creates a board as specified by a load game file
 *
 * @param height
 *      The number of rows in the board
 *
 * @param width
 *      The number of columns in the board
 *
 * @return A board with dimensions height*width
 */
Board* load_board(int height, int width) {
    Board* board = (Board*) malloc(sizeof(Board));
    board->height = height;
    board->width = width;
    board->widthLastIndex = board->width - 1;
    board->heightLastIndex = board->height - 1;
    board->board = (int**) malloc(sizeof(int*) * board->height);
    for(int i = 0; i < board->height; i++) {
        board->board[i] = (int*) malloc(sizeof(int) * board->width);
    }
    return board;
}

/**
 * @brief Checks that the grid in a load game file has the correct number
 * of rows, columns and content. Terminates the program if not.
 *
 * @param fileContent
 *      The content of a loaded file
 *
 * @param game
 *      The game with a board the grid is to be copied to
 */
void check_grid(char* fileContent, Game* game) {
    char* grid;
    grid = get_grid(fileContent);
    check_rows(grid, game->board->height);
    check_width(grid, game->board->width);
    check_grid_content(grid);
}

/**
 * @brief Returns the grid in a load file
 *
 * @param fileContent
 *      The content of a load file
 *
 * @return The grid in fileContent
 */
char* get_grid(char* fileContent) {
    int index = get_line_index(fileContent, 1);
    char valueAtIndex = fileContent[index];
    int size = get_grid_size(fileContent, index);
    char* grid = (char*) malloc(sizeof(char) * size);
    int count = 0;
    while(valueAtIndex != '\0') {
        valueAtIndex = fileContent[index];
        grid[count] = valueAtIndex;
        count += 1;
        index += 1;
    }
    return grid;
}

/**
 * @brief Terminates the program if there are not enough rows in the grid
 *
 * @param grid
 *      The grid whose rows will be counted
 *
 * @param rows
 *      The number of rows the grid should have
 */
void check_rows(char* grid, int rows) {
    int total = count_this_char(grid, '\n');
    if(total != rows) {
        error_handle(5);
    }
}

/**
 * @brief Terminates the program if there are not enough columns in the grid
 *
 * @param grid
 *      The grid whose columns will be counted
 *
 * @param column
 *      The number of columns the grid should have
 */
void check_width(char* grid, int column) {
    int index = 0;
    int count = 0;
    char valueAtIndex = 'a';
    while(valueAtIndex != '\0') {
        valueAtIndex = grid[index];
        if(valueAtIndex == '\n') {
            if(count != column) {
                error_handle(5);
            }
            count = 0;
        } else {
            count += 1;
        }
        index += 1;
    }
}

/**
 * @brief Terminates the program if the grid contains anything other than
 * '.' or 'X' or 'O', or new lines.
 *
 * @param grid
 *      The grid to be inspected
 */
void check_grid_content(char* grid) {
    int index = 0;
    char valueAtIndex = 'a';
    while(valueAtIndex != '\0') {
        valueAtIndex = grid[index];
        if(valueAtIndex != '.' && valueAtIndex != 'X' && valueAtIndex != 'O' 
                && valueAtIndex != '\n' && valueAtIndex != '\0') {
            error_handle(5);
        }
        index += 1;
    }
}

/**
 * @brief Returns the size of the grid loaded in from a saved game
 *
 * @param fileContent
 *      The content of a saved game
 *
 * @param index
 *      The postion where the grid starts
 * 
 * @return the size of the grid
 */
int get_grid_size(char* fileContent, int index) {
    char valueAtIndex = fileContent[index];
    int count = 0;
    while(valueAtIndex != '\0') {
        valueAtIndex = fileContent[index];
        index += 1;
        count += 1;
    }
    return count;
}

/**
 * @brief Returns the content of file whose size is not known
 *
 * @param file
 *      The file to be read in
 *
 * @return The content of a file
 */
char* read_in_file_content(FILE* file) {
    int size = get_file_size(file);
    char* buffer = get_file_content(file, size);
    fclose(file);
    return buffer;
}

/**
 * @brief Returns the number of characters in a file
 *
 * @param file
 *      The file whose content will be counted
 *
 * @return
 *      The number of characters in the file
 */
int get_file_size(FILE* file) {
    int count = 0;
    char fileContent = 'a';
    while(fileContent != EOF) {
        fileContent = fgetc(file);
        count += 1;
    }
    fseek(file, 0, SEEK_SET);
    return count;
}

/**
 * @brief Returns the content of a file whose size is known
 *
 * @param size
 *      The number of characters in the file
 *
 * @param file
 *      The file to be read in
 *
 * @return the content of the file
 */
char* get_file_content(FILE* file, int size) {
    int i;
    char* buffer = (char*) malloc(sizeof(char) * size);
    for(i = 0; i < size - 1; i++) {
        buffer[i] = fgetc(file);
    }
    buffer[i] = '\0';
    return buffer;
}

/**
 * @brief Splits a line along it's whitespace
 *
 * @param firstLine
 *      The line to split
 *
 * @param target
 *      The number of words in the line
 *
 * @return The line split along it's whitespace
 */
char** split_string(char* firstLine, int target) {
    char** splitLine = (char**) malloc(sizeof(char*) * target);
    char* breakPoint;
    for(int i = 0; i < target; i++) {
        splitLine[i] = (char*) malloc(sizeof(char*) * strlen(firstLine));
    }
    breakPoint = strtok(firstLine, " ");
    int i = 0;
    while (breakPoint != NULL) {
        splitLine[i] = breakPoint;
        i += 1;
        breakPoint = strtok(NULL, " ");
    }
    return splitLine;
}

/**
 * @brief Converts a collection of numbers into integers
 *
 * @param strings
 *      The numbers to be converted
 *
 * @param target
 *      The number of numbers to be converted
 *
 * @return The integer equivilant to the numbers provided
 */
int* convert_str_to_int(char** strings, int target) {
    int* integers = (int*) malloc(sizeof(int) * target);
    for(int i = 0; i < target; i++) {
        integers[i] = strtol(strings[i], NULL, 10);
    }
    return integers;
}

/**
 * @brief Creates a new game according to command-line arguments
 *
 * @param arguments
 *      The command-line arguments
 *
 * @return A new game created according to the command-line arguments
 */
Game* create_new_game(char** arguments) {
    Game* game = (Game*) malloc(sizeof(Game));
    game->board = create_board(arguments);
    game->oLost = false;
    game->xLost = false;
    clean_board(game->board);
    game->players = create_players(arguments);
    game->currentPlayer = 0;
    initialize_ai_moves(game);
    return game;
} 

/**
 * @brief Creates a board according to command-line arguments
 *
 * @param arguments
 *      The command-line arguments
 *
 * @return The board according to the command-line arguments
 * */
Board* create_board(char** arguments) {
    Board* board = (Board*) malloc(sizeof(Board));
    errno = 0;
    board->height = strtol(arguments[3], NULL, 10);
    board->width = strtol(arguments[4], NULL, 10);
    board->widthLastIndex = board->width - 1;
    board->heightLastIndex = board->height - 1;
    board->board = (int**) malloc(sizeof(int*) * board->height);
    for(int i = 0; i < board->height; i++) {
        board->board[i] = (int*) malloc(sizeof(int) * board->width);
    }
    return board;
}

/**
 * @brief 0's out the contents of a board
 *
 * @param board
 *      The board to be 0'd out
 */
void clean_board(Board* board) {
    for(int i = 0; i < board->height; i++) {
        for(int j = 0; j < board->width; j++) {
            board->board[i][j] = LIBERTY;
        }
    }
}

/**
 * @brief Prints out the board for gameplay
 *
 * @param board
 *      The board to be printed
 */
void display_board(Board* board) {
    // Prints header
    printf("/");
    for(int x = 0; x < board->width; x++) {
        printf("-");
    }
    // Prints board body
    printf("%c\n", 92);
    for(int i = 0; i < board->height; i++) {
        printf("|");
        for(int j = 0; j < board->width; j++) {
            char symbol;
            if(board->board[i][j] == LIBERTY) {
                symbol = '.';
            } else if(board->board[i][j] == O_STONE) {
                symbol = 'O';
            } else {
                symbol = 'X';
            }
            printf("%c", symbol);
        }
        printf("|");
        printf("\n");
    }
    // Prints footer
    printf("%c", 92);
    for(int y = 0; y < board->width; y++) {
        printf("-");
    }
    printf("/\n");
}

/**
 * @brief Prints out a board for debugging, where the values of the board
 * will be printed
 *
 * @param board
 *      The board to be printed
 */
void display_board_debug(Board* board) {
    // Prints header
    printf("/");
    for(int x = 0; x < board->width; x++) {
        printf("-");
    }
    printf("%c\n", 92);
    // Prints board body
    for(int i = 0; i < board->height; i++) {
        printf("|");
        for(int j = 0; j < board->width; j++) {
            printf("%d", board->board[i][j]);
        }
        printf("|");
        printf("\n");
    }
    // Prints footer
    printf("%c", 92);
    for(int y = 0; y < board->width; y++) {
        printf("-");
    }
    printf("/\n");
}

/**
 * @brief Creates players with the types specified by command-line arguments
 *
 * @param arguments
 *      The command-line arguments
 *
 * @return The players according to the command-line specifications
 */
Player** create_players(char** arguments) {
    Player** players = (Player**) malloc(sizeof(Player*) * 2);
    Player* player0 = (Player*) malloc(sizeof(Player));
    Player* player1 = (Player*) malloc(sizeof(Player));
    if(arguments[1][0] == 'h') {
        player0->type = HUMAN; 
    } else {
        player0->type = COMPUTER;
    }
    if(arguments[2][0] == 'h') {
        player1->type = HUMAN;
    } else {
        player1->type = COMPUTER;
    }
    players[0] = player0;
    players[1] = player1;
    return players;
}

/**
 * @brief Begins gameplay
 *
 * @param game
 *      The game to be played
 */
void run_game(Game* game) {
    while(1) {
        display_board(game->board);
        make_move(game);
        check_for_captures(game);
        change_current_player(game);
    }
}

/**
 * @brief Prints out the player prompt for the current player in the game
 *
 * @param game
 *      The game
 */
void display_turn(Game* game) {
    printf("Player ");
    int currentPlayer = game->currentPlayer;
    if(currentPlayer == 0) {
        printf("O");
    } else {
        printf("X");
    }
    if(game->players[currentPlayer]->type == COMPUTER) {
        printf(": ");
    } else {
        printf("> ");
    }
}

/**
 * @brief Makes a move for the current player
 *
 * @param game
 *      The game in session
 */
void make_move(Game* game) {
    int currentPlayer = game->currentPlayer;
    if(game->players[currentPlayer]->type == COMPUTER) {
        make_ai_move(game);
    } else {
        make_player_move(game);
    }
}

/**
 * @brief Makes a move for a computer player
 *
 * @param game
 *      The game in session
 */
void make_ai_move(Game* game) {
    display_turn(game);
    while(!position_unoccupied(game)) {
        generate_ai_move(game);
    }
    int playerIndex = game->currentPlayer;
    int row = game->players[playerIndex]->rowToMove;
    int column = game->players[playerIndex]->columnToMove;
    place_stone(playerIndex, row, column, game);
    printf("%d %d\n", game->players[game->currentPlayer]->rowToMove, 
            game->players[game->currentPlayer]->columnToMove); 
    generate_ai_move(game);
}

/**
 * @brief Changes the current player to the other player
 *
 * @param game
 *      The current game that is in session, which contains players
 */
void change_current_player(Game* game) { 
    if(game->currentPlayer == 1) {
        game->currentPlayer = 0;
    } else {
        game->currentPlayer = 1;
    }
}

/**
 * @brief Sets the initial conditions for each computer player when a new game
 * is invoked.
 *
 * @param game
 *      A game that has been created in the session the user is playing in
 */
void initialize_ai_moves(Game* game) {
    for(int i = 0; i < 2; i++) {
        Player* player = game->players[i];
        if(i == 0) {
            player->initialRow = 1;
            player->initialColumn = 4;
            player->multiplicationFactor = 29;
            player->gridWidth = game->board->width;
            player->gridHeight = game->board->height;
            player->moveCounter = 1;
            player->spacer = player->initialRow * player->gridWidth 
                    + player->initialColumn;
            player->instantiatedRow = player->initialRow;
            player->instantiatedColumn = player->initialColumn;
            player->rowToMove = player->instantiatedRow 
                    % player->gridHeight;
            player->columnToMove = player->instantiatedColumn 
                    % player->gridWidth;
        } else { 
            player->initialRow = 2;
            player->initialColumn = 10;
            player->multiplicationFactor = 17;
            player->gridWidth = game->board->width;
            player->gridHeight = game->board->height;
            player->moveCounter = 1;
            player->spacer = player->initialRow * player->gridWidth +
                    player->initialColumn;
            player->instantiatedRow = player->initialRow;
            player->instantiatedColumn = player->initialColumn;
            player->rowToMove = player->instantiatedRow 
                    % player->gridHeight;
            player->columnToMove = player->instantiatedColumn 
                    % player->gridWidth;
        }
    }
}

/**
 * @brief Gets a valid move from the player and palces a stone in that
 * position
 *
 * @param game
 *      The game in session
 *
 */
void make_player_move(Game* game) {
    int* parsedInput = get_valid_player_move(game);
    int playerIndex = game->currentPlayer;
    place_stone(playerIndex, parsedInput[0], parsedInput[1], game);
    free(parsedInput);
}

/**
 * @brief Continuously prompts the user for input until the user enters a valid
 * move.
 *
 * @param game
 *      The game in session
 *
 * @return A valid player move
 */
int* get_valid_player_move(Game* game) {
    int* parsedInput;
    while(1) {
        display_turn(game);
        char* rawInput = get_raw_input(72);
        if(rawInput[0] == 'w') {
            save_game(game, rawInput);
        }
        if(!is_user_move_structurally_correct(rawInput)) {
            continue;
        }
        char** unparsedInput = split_string(rawInput, 2);
        parsedInput = convert_str_to_int(unparsedInput, 2); 
        if(!is_user_move_in_board(parsedInput, game->board)) {
            continue;
        }
        if(is_unoccupied(parsedInput, game)) {
            break;
        }
    }
    return parsedInput;
}

/**
 * @brief Returns true if the user move is within the bounds of the board
 *
 * @param userMove
 *      The user's move
 *
 * @param board
 *      The board the user's move must be within
 *
 * @return True if the user's move is within the bounds of the board, false
 * otherwise
 */
bool is_user_move_in_board(int* userMove, Board* board) {
    bool isValid = true;
    isValid &= check_valid_range(userMove[0], 0, board->heightLastIndex);
    isValid &= check_valid_range(userMove[1], 0, board->widthLastIndex);
    return isValid;
}

/**
 * @brief Returns true if user input structurally correct
 *
 * @param userMove
 *      Move input from the user
 *
 * @return True if the input has only 1 space, is less than 70 characters long,
 * has exactly 2 arguments, and consists only of integers
 */
bool is_user_move_structurally_correct(char* userMove) { 
    bool isValid = true;
    isValid &= is_input_size_correct(userMove, 71);
    isValid &= (count_the_words(userMove) == 2);
    isValid &= (count_this_char(userMove, ' ') == 1);
    isValid &= check_if_only_numbers(userMove);
    return isValid;
}

/**
 * @brief Returns true if value is between min and max inclusive, and false
 * otherwise.
 *
 * @param value
 *      The value to be measured
 * 
 * @param min
 *      The smallest that value can be for the function to return true
 *
 * @param max
 *      The largest that value can be for the function to return true
 *
 * @return True if min <= value <= max
 */
bool check_valid_range(int value, int min, int max) {
    if(value < min || value > max) {
        return false;
    }
    return true;
}

/**
 * @brief Takes a coordinate and returns true if it is unoccupied 
 *
 * @param parsedInput
 *      The coordinates
 *
 * @param game
 *      The game in session containing the board
 *
 * @return True if and only if the coordinate is not occupied
 */
bool is_unoccupied(int* parsedInput, Game* game) {
    if(game->board->board[parsedInput[0]][parsedInput[1]] != 0) {
        return false;
    }
    return true;
}

/**
 * @brief Copies user input into a buffer until a newline character is reached,
 * or an EOF where the program will terminate. If the user input exceeds the
 * maximum size of the buffer, the remainder of the input will be flushed until
 * a newline character, or until EOF where the program will terminate.
 *
 * @param maxSize
 *      The maximum size for user input before flushing occurs
 *
 * @return A buffer filled with the user input.
 */
char* get_raw_input(int maxSize) {
    int index = 0;
    char valueAtIndex;
    char* buffer = (char*) malloc(sizeof(char) * 72);
    while(1) {
        valueAtIndex = getc(stdin);
        if(valueAtIndex == EOF) {
            error_handle(6);
        }
        if(valueAtIndex == '\n') {
            break;
        }
        if(index == maxSize - 1) {
            flush_input();
            buffer[maxSize - 1] = '\0';
            break;
        }
        buffer[index] = valueAtIndex;
        index += 1;
    }
    buffer[index] = '\0';
    return buffer;
}

/**
 * @brief Returns true if the size of the input is greater than or equal to
 * the max
 *
 * @param max
 *      The maximum size of the input for this to return true
 * 
 * @param input
 *      The input to be measured
 *
 * @return True if and only if the size of the input is less than or equal to
 * max
 */
bool is_input_size_correct(char* input, int max) {
    if(strlen(input) >= max) {
        return false;
    }
    return true;
}

/**
 * @brief Clears the source of input to this program until a newline character
 * is reached. If EOF is hit before a newline character, this function will
 * terminate
 */
void flush_input() {
    char target;
    while((target = getc(stdin)) != '\n') {
        if(target == EOF) {
            error_handle(6);
        }
    }
}

/**
 * @brief Generates a move for the current player, and increments the moves 
 * generated by that player
 *
 * @param game
 *      The game which is currently in session, which contains a board and
 *      2 players
 */
void generate_ai_move(Game* game) {
    Player* player = game->players[game->currentPlayer];
    if(player->moveCounter % 5 == 0) {
        int n = (player->spacer + player->moveCounter / 5 * 
                player->multiplicationFactor) % 1000003; 
        player->instantiatedRow = n / player->gridWidth;
        player->instantiatedColumn = n % player->gridWidth;
    } else if(player->moveCounter % 5 == 1) {
        player->instantiatedRow += 1;
        player->instantiatedColumn += 1;
    } else if(player->moveCounter % 5 == 2) {
        player->instantiatedRow += 2;
        player->instantiatedColumn += 1;
    } else if(player->moveCounter % 5 == 3) {
        player->instantiatedRow += 1;
        player->instantiatedColumn += 0;
    } else if(player->moveCounter % 5 == 4) {
        player->instantiatedRow += 0;
        player->instantiatedColumn += 1;
    }
    player->rowToMove = player->instantiatedRow % player->gridHeight;
    player->columnToMove = player->instantiatedColumn % player->gridWidth;
    player->moveCounter += 1;
}

/** 
 * @brief Places a stone on the board for the current player
 *
 * @param game
 *      The game which is currently in session, which contains a board and
 *      2 players.
 * 
 * @param playerIndex
 *      The index of the player
 *
 * @param rowTarget
 *      The row to place the stone
 *
 * @param columnTarget
 *      The column to place the stone
 */
void place_stone(int playerIndex, int rowTarget, int columnTarget, 
        Game* game) {
    game->board->board[rowTarget][columnTarget] = game->currentPlayer + 2;
}

/**
 * @brief Takes a game and returns true if and only if the position on the 
 * game's board the current player in the game is trying to place their stone 
 * onto is unoccupied.
 *
 * @param game
 *     The game which is currently in session, which contains a board
 *     and players
 *
 * @return true if the position the currently player in the game is trying
 * to occupy has not already been occupied
 */
bool position_unoccupied(Game* game) {
    int playerIndex = game->currentPlayer;
    int row = game->players[playerIndex]->rowToMove;
    int column = game->players[playerIndex]->columnToMove;
    if(game->board->board[row][column] == LIBERTY) {
        return true;
    } else {
        return false;
    }
}

/**
 * @brief Follows every string on the board, checking for captured strings. If
 * a string is captured, the program will terminate.
 *
 * @param game
 *      The game currently in session
 */
void check_for_captures(Game* game) {
    for(int i = 0; i < game->board->height; i++) {
        for(int j = 0; j < game->board->width; j++) {
            // If a string has been reached, check it for completions
            if(game->board->board[i][j] == O_STONE || game->board->board[i][j] 
                    == X_STONE) {
                int owner = game->board->board[i][j];
                // If a string has been completed, record whose string it was
                if (!check_string_completion(game, i, j)) {
                    put_stones_back(game, owner);
                    if(owner == 2) {
                        game->oLost = true;
                    } else {
                        game->xLost = true;
                    }
                }
                put_stones_back(game, owner);
            }
        }
    }
    crown_winner(game);
}

/**
 * @brief Exits the game if a player has lost
 *
 * @param game
 *      The game in session
 */
void crown_winner(Game* game) {
    if(game->oLost && !game->xLost) {
        display_board(game->board);
        printf("Player X wins\n");
        exit(0);
    } else if (!game->oLost && game->xLost) {
        display_board(game->board);
        printf("Player O wins\n");
        exit(0);
    } else if (game->oLost && game->xLost) {
        if(game->currentPlayer == 0) {
            display_board(game->board);
            printf("Player O wins\n");
            exit(0);
        } else {
            display_board(game->board);
            printf("Player X wins\n");
            exit(0);
        }
    }
}

/**
 * @brief Iterates through the board in the game, replacing all 1's with the
 * stone of the owner of that position in the game.
 *
 * @param game
 *      The game in session
 *
 * @param owner
 *      The stone of the owner, as represented on a board.
 */
void put_stones_back(Game* game, int owner) {
    for(int i = 0; i < game->board->height; i++) {
        for(int j = 0; j < game->board->width; j++) {
            if(game->board->board[i][j] == 1) {
                game->board->board[i][j] = owner;
            }
        }
    }
}

/**
 * @brief Takes in a stone that is a member of a string (as defined in the 
 * game, not in C), and visits every stone in that string, returning true if 
 * any of them have a remaining liberty, and false if they do not.
 *
 * @param game
 *      The game in session
 *
 * @param i
 *      The row the stone in the string is
 *
 * @param j
 *      The column the stone in the string is
 *
 * @return true if there exists a stone in the string that is at position
 * [i][j] on the board is in, with a liberty, and false otherwise.
 *
 */
bool check_string_completion(Game* game, int row, int column) {
    int targetNumber = game->board->board[row][column]; 
    Board* board = game->board;
    board->board[row][column] = 1;
    bool status = false;
    bool currentStatus = liberties_present(game, row, column);
    // Check an adjacent square only if it exists
    if(row > 0) {
        if(board->board[row - 1][column] == targetNumber) {
            bool pastStatus = check_string_completion(game, row - 1, column);
            status |= currentStatus || pastStatus;
        }
    }
    if(row < board->height - 1) {
        if(board->board[row + 1][column] == targetNumber) {
            bool pastStatus = check_string_completion(game, row + 1, column);
            status |= currentStatus || pastStatus;
        }
    }
    if(column < board->width - 1) {
        if(board->board[row][column + 1] == targetNumber) {
            bool pastStatus = check_string_completion(game, row, column + 1);
            status |= currentStatus || pastStatus;
        }
    }
    if(column > 0) {
        if(board->board[row][column - 1] == targetNumber) {
            bool pastStatus = check_string_completion(game, row, column - 1);
            status |= currentStatus || pastStatus;
        }
    }
    return status |= liberties_present(game, row, column);
}

/**
 * @brief Takes in a position of a stone on the board in the game, and returns
 * true if there is a position adjacent to that stone which is empty, and
 * returns false if there is no such position.
 *
 * @param game
 *      The game in session
 *
 * @param i
 *      The row the stone to be inspected is in
 *
 * @param j
 *      The column the stone to be inspected is in
 *
 * @return true if postion [i][j] on the board in game has liberties, false
 * if it has no liberties
 */
bool liberties_present(Game* game, int row, int column) {
    Board* board = game->board;
    // Only check the adjacent position if it exists
    if(row > 0) {
        if(board->board[row - 1][column] == LIBERTY) {
            return true;
        }
    }
    if(row < board->height - 1) {
        if(board->board[row + 1][column] == LIBERTY) {
            return true;
        }
    }
    if(column < board->width - 1) {
        if(board->board[row][column + 1] == LIBERTY) {
            return true;
        }
    }
    if(column > 0) {
        if(board->board[row][column - 1] == LIBERTY) {
            return true;
        }
    }
    return false;
}

/**
 * @brief Saves a game to a file
 *
 * @param game
 *      The game that will be saved to a file
 *
 * @param userInput
 *      The user command requesting a save
 *
 * @return 0 if the function executes correctly
 */
int save_game(Game* game, char* userInput) {
    if(!is_input_size_correct(userInput, 71)) {
        error_handle(7);
        return 1;
    }
    if(!check_location(userInput)) {
        error_handle(7);
        return 1;
    }
    char* filePath = get_file_path(userInput);
    write_to_save_file(game, filePath);
    free(filePath);
    return 0;
}

/**
 * @brief Returns true if the file path in a user command to save represents
 * a valid path to a file
 *
 * @param userInput
 *      The user command requesting a save
 *
 * @return True if and only if the user command contains a valid path to save
 * to
 */
bool check_location(char* userInput) {
    char* filePath = get_file_path(userInput);
    FILE* file;
    if ((file = fopen(filePath, "w"))) {
        fclose(file);
        free(filePath);
        return true;
    }
    free(filePath);
    return false;
}

/**
 * @brief Returns the file path of a user command requesting to save
 *
 * @param userInput
 *      The user command requesting to save
 *
 * @return The file path in the user command to save
 */
char* get_file_path(char* userInput) {
    char* filePath = (char*) calloc(71, sizeof(char) * 71);
    char valueAtIndex = 'a';
    int index = 1;
    while(valueAtIndex != '\0') {
        valueAtIndex = userInput[index];
        filePath[index - 1] = valueAtIndex;
        index += 1;
    }
    filePath[index - 1] = '\0';
    return filePath;
}

/**
 * @brief Copies the file contents into a save game
 *
 * @param game
 *      The game whose save game details will be copied
 *
 * @param filePath
 *      The location of the file to be written to
 */
void write_to_save_file(Game* game, char* filePath) {
    FILE* file = fopen(filePath, "w");
    write_header(file, game);
    write_board(file, game);
    fclose(file);
}

/**
 * @brief Copies the game detail header of the game in play to a file
 *
 * @param file
 *      The file the details will be copied to
 *
 * @param game
 *      The game whose details will be copied
 */
void write_header(FILE* file, Game* game) {
    fprintf(file, "%d %d ", game->board->height, game->board->width);
    fprintf(file, "%d ", game->currentPlayer);
    write_computer_move(file, game);
}

/**
 * @brief Copies the computer move of the game in play to a file
 *
 * @param file
 *      The file the details will be written to
 *
 * @param game
 *      The game whose computer move details will be copied to
 */
void write_computer_move(FILE* file, Game* game) {
    fprintf(file, "%d ", game->players[0]->rowToMove);
    fprintf(file, "%d ", game->players[0]->columnToMove);
    fprintf(file, "%d ", game->players[0]->moveCounter - 1);
    fprintf(file, "%d ", game->players[1]->rowToMove);
    fprintf(file, "%d ", game->players[1]->columnToMove);
    fprintf(file, "%d\n", game->players[1]->moveCounter - 1);
}

/**
 * @brief Copies the board in play to a file
 *
 * @param file
 *      The file the board will be copied to
 *
 * @param game
 *      The game whose board will be copied
 */
void write_board(FILE* file, Game* game) {
    Board* board = game->board;
    for(int i = 0; i < board->height; i++) {
        for(int j = 0; j < board->width; j++) {
            if(board->board[i][j] == LIBERTY) {
                fprintf(file, ".");
            } else if(board->board[i][j] == O_STONE) {
                fprintf(file, "O");
            } else if(board->board[i][j] == X_STONE) {
                fprintf(file, "X");
            }
        }
        fprintf(file, "\n");
    }
}

/**
 * @brief Displays an appropriate message given an error code, and exists
 * if that error code requires it
 *
 * @param errorCode
 *      The error code that corresponds to the event that caused this function
 *      to be called
 */
void error_handle(int errorCode) {
    if (errorCode == 1) {
        fprintf(stderr, "Usage: nogo p1type p2type"
                " [height width | filename]\n");
        exit(1);
    } else if (errorCode == 2) {
        fprintf(stderr, "Invalid player type\n");
        exit(2);
    } else if (errorCode == 3) {
        fprintf(stderr, "Invalid board dimension\n");
        exit(3);
    } else if (errorCode == 4) {
        fprintf(stderr, "Unable to open file\n");
        exit(4);
    } else if (errorCode == 5) {
        fprintf(stderr, "Incorrect file contents\n");
        exit(5);
    } else if (errorCode == 6) {
        fprintf(stderr, "End of input from user\n");
        exit(6);
    } else if (errorCode == 7) {
        fprintf(stderr, "Unable to save game\n");
    }
}
