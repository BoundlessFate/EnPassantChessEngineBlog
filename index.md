---
title: Home
layout: home
---

This blog will go over the way I created my Chess Engine "En Passant Chess Engine"
Each numbered sections acts almost as a new "era" with my engine. 
I will discuss what issues I faced, how I overcame them, and any issues I am currently facing
I will also go over implementation changes since the last section

[GitHub](https://github.com/BoundlessFate/EnPassantChessEngine)

---
title: 1. Player Vs. Player
layout: default
/1PlayerVsPlayer/
---

The first order of business when creating this chess engine is making a gui. 
I decided on using c# for my frontend development because I find it provides a strong balance between speed and functionality.
Code in C# isn't difficult to mock up and it isn't unusably slow, even for Chess Engine standards.
I decided for the frontend to utilize C# WinForms over WPF becaues the layout and design of the application does not need to be sleek and new.
It has to be functional and fast to create which made WinForms the perfect tool for the job.
I also decided that for each implementation of a new version of the engine, I would create a separate c++ .dll which handles that specific version.
This would allow me to pit previous versions of the engine against either the player or anothre version of the engine.

Okay... Now that the frontend was created using a series of 64 buttons (yes it was tedious but got the job done) I needed a data representation
Since the c# heavylifting only includes move calculations for humans and not any searching, it doesn't have to be perfectly optimal.
I decided on a char[][] array of size 8x8 where each character represents a piece. 
A space is no piece, k is king, b is bishop, r is rook, p is pawn, n is knight, q is queen. A capital letter means White and a lowercase is Black,

For the most part move generation was simple to implement. To get moves, since this code only is focused on the frontend, 
I only calculate the moves for a selected piece. Once they are calculated, the spaces available turn green.
I had a different function for each piece type, and it returned coordinates of spaces it could move to. The two problematic moves were castling and en passant, but
they were a non issue with some extra data storage for if pieces had moved and when.

Checks were annoying to get, but I implemented it in such a way that it took care of pins and other edge cases. For each move you could make, it made the move,
and checked if any of the opponents moves are to take the king, and unmade the move. If the opponent can take the king, then you are in check.

End of game was calculated when a player has no moves left. 
From there it would flip the player and see if they can take the king, which would differentiate between stalemate and checkmate.

Promotions also required a bit of new code and frontend work. When a pawn reaches its ending square, the move changes to -1 or -2 signifying the game is waiting
on a player to promote their piece. Once they click on the promotion buttons it goes back to normal with the new piece.

After getting piece generation done I focused on the edge cases for stalemates, like 50 moves without a pawn push or capture, threefold repetition, and draw by
insufficient material. These were not very hard to implement on their own.

My most pressing issue currently is that I have no way to verify if the move generation is perfect. To verify, I would have to create a perft() function which
checks a given position to a given depth to see how many leaf nodes it found with the depth given. My code currently piggybacks heavily off the buttons themselves to perform actions.

I don't actually have a function that is MakeMove or UnmakeMove. It is all handled from the button press action in c# which is very clunky but works for human players.

To verify that this works I will need to write the first c++ bot. Although tedious, I already have the move generation completed, so it is just a matter of translation and some tweaking.

Once the c++ dll is created, I will have to integrate it into the frontend. I think it would be nicer to have an instance of the engine running to calculate moves instead of 
holding everything up by the clunky frontend code I have now. Splitting move generation into the engine would clean up the frontend code significantly 
(and cause some speed ups even though its not needed for the frontend at the moment)
[See This Commit](https://github.com/BoundlessFate/EnPassantChessEngine/tree/f1e82463a54fa23de855a1c1d2086a65a952c5b6)
[GitHub](https://github.com/BoundlessFate/EnPassantChessEngine)