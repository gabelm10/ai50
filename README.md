"""
Tic Tac Toe Player
"""

import math
import copy


X = "X"
O = "O"
EMPTY = None


def initial_state():
    """
    Returns starting state of the board.
    """
    return [[EMPTY, EMPTY, EMPTY],
            [EMPTY, EMPTY, EMPTY],
            [EMPTY, EMPTY, EMPTY]]


def player(board):
    """
    Returns player who has the next turn on a board.
    """
    
    #Counts the number of empty squares
    num_empty = board[0].count(EMPTY)+board[1].count(EMPTY)+board[2].count(EMPTY)
    
    #On a even number of empty squares it is O's turn to move
    if num_empty % 2 == 0:
        return O
    else:
        return X

def actions(board):
    """
    Returns set of all possible actions (i, j) available on the board.
    """
    
    #variable used to keep track of possible moves
    pos_moves = set()
    
    #Adds any empty squares to the list of possible moves
    for row in range(0,3):
        for column in range(0,3):
            if board[row][column] == EMPTY:
                pos_moves.add((row, column))
   
    return pos_moves

def result(board, action):
    """
    Returns the board that results from making move (i, j) on the board.
    """
    
    #If board space for the action is not empty return error message
    if board[action[0]][action[1]] != EMPTY:
        raise AttributeError
    
    #Creates a copy of the board where action will be taken
    new_board = copy.deepcopy(board)
    
    #Take action
    new_board[action[0]][action[1]] = player(board)
    
    return new_board
    
def winner(board):
    """
    Returns the winner of the game, if there is one.
    """
    
    #Checks for a horizontal win
    for row in board:
        #Checks if there is an empty value in the row
        if not EMPTY in row:
            #Checks if there are three equal values in a row
            if row.count(row[0]) == 3:
                return row[0]
    
    #Checks for a vertical win
    for i in range(0,3):
        if board[0][i] != EMPTY:
            if board[1][i] == board[0][i] and board[2][i] == board[0][i]:
                return board[0][i]
    
    #Checks for a diagonal win
    if board[1][1] != EMPTY:
        #Checks both diagonals individually
        if board[0][0] == board[1][1] and board[2][2] == board[1][1]:
            return board[1][1]
        if board[0][2] == board[1][1] and board[2][0] == board[1][1]:
            return board[1][1]
    
    #Return no winner
    return None
        
def terminal(board):
    """
    Returns True if game is over, False otherwise.
    """

    #If there is a winner then game ends    
    if winner(board) != None:
        return True
    
    #Counts the number of empty squares
    num_empty = board[0].count(EMPTY)+board[1].count(EMPTY)+board[2].count(EMPTY)
    #If there are no empty squares, game is a draw
    if num_empty == 0:
        return True
    
    return False

def utility(board):
    """
    Returns 1 if X has won the game, -1 if O has won, 0 otherwise.
    """
    end_game = winner(board)
    
    if end_game == X:
        return 1
    elif end_game == O:
        return -1
    else:
        return 0


def minimax(board):
    """
    Returns the optimal action for the current player on the board.
    """
    #If it is a terminal board then there are no moves to make
    if terminal(board):
        return None
    
    #Contains the optimal move for the current player
    optimal_move = None
    
    #Check whether current player is a maximizing (X) or minimizing (O) player
    if player(board) == X:
        
        #Initialize the best score possible as the worst you could do
        best_score = -2
        
        #Check all the actions possible
        for action in actions(board):
            
            #The current action's score is the score a min player would give if you took the action
            action_score = minplayer(result(board,action),-2)
            
            #If the score is better than the current score then update optimal move
            if best_score < action_score:
                best_score = action_score
                optimal_move = action
                #1 is the best result possible, therefore if it is found just return it
                if best_score == 1:
                    return optimal_move
        
    else:
        #Initialize the best score possible as the worst you could do
        best_score = 2
        
        #Check all the actions possible
        for action in actions(board):
            
            #The current action's score is the score a max player would give if you took the action
            action_score = maxplayer(result(board,action),2)
            
            #If the score is better than the current score then update optimal move
            if best_score > action_score:
                best_score = action_score
                optimal_move = action
                #-1 is the best result possible, therefore if it is found just return it
                if best_score == -1:
                    return optimal_move
        
    return optimal_move

def maxplayer(board, vmin):
    """
    Returns the optimal action for a maximizing player on the board.
    """
    #If it is a terminal board, then end of tree is reached
    if terminal(board):
        return utility(board)
    
    #V is the current maximum value the player has to choose from
    #Initially v is set to the lowest possible value
    v = -2
    
    #Loop through possible actions to find action that maximizes v
    for action in actions(board):
        #v is maximized between previous v and value a minimizing player would choose
        v = max(v, minplayer(result(board, action), v))
        
        if vmin<v:
            next
        
        #Best score possible is 1
        if v == 1:
            return v

    return v

def minplayer(board, vmax):
    """
    Returns the optimal action for a minimizing player on the board.
    """
    
    #If it is a terminal board, then end of tree is reached
    if terminal(board):
        return utility(board)
    
    #V is the current minimum value the player has to choose from
    #Initially v is set to the highest possible value
    v = 2
    
    #Loop through possible actions to find action that minimizes v
    for action in actions(board):
        #v is minimized between previous v and value a maximizing player would choose
        v = min(v, maxplayer(result(board, action), v))
        
        if vmax>v:
            next
        
        #Best score possible is 1
        if v == -1:
            return v

    return v
