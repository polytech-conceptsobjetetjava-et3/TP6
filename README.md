Programmation Java @ Et3
<br>
Polytech Paris-Saclay | 2019-20

___

# TP6

On souhaite mettre en place un système de commentaires très simple sur un texte statique. Concrètement, un lecteur doit pouvoir ajouter un commentaire libre portant sur un fragment continu d'un document. Les commentaires collectés pourront ensuite être affichés selon différentes stratégies de tri.

Un commentaire pour un texte donné sera donc composé d'un utilisateur (*user*), d'un indice de caractère de début (*indexBegin*) et de fin (*indexEnd*), ainsi que d'une date de création (*creationDate*).

Un certain nombre de stratégies de tri devront être anticipées, notamment :
- afficher les commentaires par ancienneté 
- afficher les commentaires par position de début dans le texte (en privilégiant les commentaires se terminant d'abord)
- afficher les commentaires par nom d'utilisateur (tri alphanumérique) puis par ancienneté
- afficher les commentaires par longueur de texte décroissante

6#1. Proposez une classe d'énumération `CommentSortingStrategy` pour représenter les stratégies de tri des commentaires. Chaque valeur d'énumération sera associée à une brève description textuelle qui sera affichée lorsqu'un tri sera réalisé.

6#1.1 code

> Ici, on veut créer une [`enum`](https://howtodoinjava.com/java/enum/enum-tutorial/#constructors) en faisant en sorte que chaque élément ait une description associée. Il faut donc implémenter un constructeur.
>
> ```Java
> package et3.java.sorting;
> 
> public enum CommentSortingStrategy
> {
> 	BySeniority("Elements are sorted from the most recent to the oldest."),
> 	ByPosition("Elements are sorted from the closest to the begining to the closest to the end."),
> 	ByNameAndSeniority("Elements are sorted by users' alphabetical order and senority, from the most recent to the oldest."),
> 	ByLength ("Elements are sorted by decreasing length.");
> 	
> 	private String description;
> 	
> 	/**
> 	 * Constructor of the enumeration {@link CommentSortingStrategy}
> 	 * @param description The description of the element
> 	 */
>     CommentSortingStrategy(String description)
>     {
>         this.description=description;
>     }
> 
>     /**
>      * This method returns the description of an element
>      * @return The description of the element
>      */
>     public String getDescription()
>     {
>         return description;
>     }
> }
> ```

6#2 Proposez un début de classe `CommentableImmutableText` pour représenter un texte non modifiable qui pourra être associé à une collection de commentaires qui sera de type `List<TextComment>` et d'implémentation associée d'un type concret approprié.

6#2.1 code

> Pour cette question, on se contente de définir les attributs de la classe et leurs méthodes d'accès et le(s) constructeur(s).
> 
> ```Java
> package et3.java.commentable;
> 
> import java.util.ArrayList;
> import java.util.List;
> 
> public class CommentableImmutableText
> {	
> 	private final String text;
> 	private List<TextComment> comments;
> 	
> 	/**
> 	 * Constructor of the class {@link CommentableImmutableText}
> 	 * @param text The commentable text
> 	 */
> 	public CommentableImmutableText(String text)
> 	{
> 		this.text = text;
> 		this.comments = new ArrayList<TextComment>();
> 	}
> 	
> 	/**
> 	 * This method returns the commentable text
> 	 * @return The commentable text
> 	 */
> 	public String getText()
> 	{
> 		return text;
> 	}
> 	
> 	/**
> 	 * This method returns the list of the comments associated with the commentable text
> 	 * @return The list of comments
> 	 */
> 	public List<TextComment> getComments()
> 	{
> 		return comments;
> 	}
> }
> ```

6#3 Proposez une définition pour le type `TextComment` sous forme de classe interne à la classe `CommentableImmutableText`. Anticipez de possibles problèmes de création de commentaires au moyen d'exceptions d'un type approprié.

6#3.1 Pourquoi le recours à une classe interne peut-il être approprié ici ?

> Si on définit une classe interne `TextComment` dans la classe englobante `CommentableImmutableText`, alors chaque instance de `TextComment` se réfère à une instance de `CommentableImmutableText`. Ces instances ont donc accès aux méthodes et aux attributs de `CommentableImmutableText`. Cela est approprié dans notre cas car les commentaires dépendent directement du texte.

6#3.2 code

> Lors de la création de commentaires, nous pourrions rencontrer plusieurs erreurs. Ici, on s'intéresse à deux exemples en particulier : le cas où l'auteur du commentaire n'a pas été explicité et le cas ou le commentaire ne réfère à aucun morceau du texte.
>
> Pour représenter ces erreurs, nous implémentons, en premier lieu, une classe mère général `TextCommentException` :
>
> ```Java
> package et3.java.exceptions;
> 
> public class TextCommentException extends Exception
> {
> 	private static final long serialVersionUID = 1L;
> 
> 	public TextCommentException()
> 	{
> 		super();
> 	}
> 	
> 	public TextCommentException(String message)
> 	{
> 		super(message);
> 	}
> }
> ```
>
> Puis, on défini nos deux classes d'erreur particulières : `AnonymousCommentException` qui interviendra lorsque l'auteur du commentaire n'est pas explicité, et `EmptyTextCommentException` qui interviendra si le commentaire créé ne se réfère à aucun morceau du text :
>
> ```Java
> package et3.java.exceptions;
> 
> public class EmptyTextCommentException extends TextCommentException
> {
> 	private static final long serialVersionUID = 1L;
> 
> 	public EmptyTextCommentException() 
> 	{
> 		super();
> 	}
> 	
> 	public EmptyTextCommentException(String message)
> 	{
> 		super(message);
> 	}
> }
> ```
> ```Java
> package et3.java.exceptions;
> 
> public class AnonymousCommentException extends TextCommentException
> {
> 	private static final long serialVersionUID = 1L;
> 
> 	public AnonymousCommentException() 
> 	{
> 		super();
> 	}
> 	
> 	public AnonymousCommentException(String message)
> 	{
> 		super(message);
> 	}
> }
> ```
>
> Une fois que ces erreurs sont définies, ont peut créer la classe `TextComment` à l'interieur de la classe `CommentableImmutableText` :
>
> ```Java
> 	public class TextComment
> 	{
> 		private String author;
> 		private Date creationDate;
> 		private int indexBegin;
> 		private int indexEnd;
> 		
> 		/**
> 		 * Constructor of the class {@link TextComment}
> 		 * @param author The author of the comment
> 		 * @param indexBegin The beginning index of the commented text
> 		 * @param indexEnd The ending index of the commented text
> 		 * @throws TextCommentException
> 		 */
> 		private TextComment(String author, int indexBegin, int indexEnd) throws TextCommentException
> 		{
> 			//The issues we can encounter are the following:
> 			
> 				//The text linked to the comment is empty
> 				if(indexBegin >= indexEnd)
> 				{
> 					throw new EmptyTextCommentException("The text linked to the comment is empty. The comment cannot be created.");
> 				}
> 				
> 				//The comment is anonymous
> 				if(author == null || author.equals(""))
> 				{
> 					throw new AnonymousCommentException("No author has been registered for this comment. The comment cannot be created.");
> 				}
> 				
> 			//If no exception raised, we implement 'TextComment'
> 			this.author = author;
> 			this.indexBegin = indexBegin;
> 			this.indexEnd = indexEnd;
> 			this.creationDate = new Date(System.currentTimeMillis());
> 		}
> 		
> 		@Override
> 		public String toString() 
> 		{
> 			StringBuilder comment = new StringBuilder("Comment : ");
> 			
> 			//We add the coordinates of the commented text
> 			comment.append("[" + indexBegin + "->" + indexEnd + "]");
> 			
> 			//We add the author's name
> 			comment.append(" Author: " + this.author);
> 			
> 			//We add the creation date
> 			comment.append(" (" + this.creationDate.toString() + ")");
> 			
> 			return comment.toString();
> 		}
> 	}
> ```

6#4 Ajoutez le code nécessaire à votre classe `CommentableImmutableText` pour réaliser l'ajout de nouveaux commentaires associés au texte, lesquels pourront être simplement ajoutés par un programme principal de test, ou par un code plus complexe permettant des ajouts interactifs par l'utilisateur.

6#4.1 code

> Pour ajouter un commentaire, on se contente de créer une nouvelle instance de `TextComment`, que l'on vient ajouter à la liste de commentaires :
> 
> ```Java
> /**
>  * This methods add a comment to the list
>  * @param author The author of the comment
>  * @param indexBegin The beginning index of the comment in the text
>  * @param indexEnd The ending index of the comment in the text
>  * @throws TextCommentException
>  */
> public void addComment(String author, int indexBegin, int indexEnd) throws TextCommentException
> {
> 	this.comments.add(new TextComment(author, indexBegin, indexEnd));
> }
> ```
> 
> Pour tester cette nouvelle méthode, on créer une classe `Main` qui fera office de porte d'entrée sur l'application, et on ajoute nos test dans la méthode `main(String[] args)` :
> 
> ```Java
> package et3.java.application;
> 
> import et3.java.commentable.CommentableImmutableText;
> import et3.java.exceptions.TextCommentException;
> 
> public class Main 
> {
> 	public static void main(String[] args) 
> 	{
> 		CommentableImmutableText commentableText = new CommentableImmutableText(
> 				"Lorem ipsum dolor sit amet, consectetur adipiscing elit. Ut tincidunt "
> 				+ "ligula ut libero egestas lobortis. Fusce rutrum nisi eget risus "
> 				+ "gravida, ut posuere eros tempus. Cras ut nibh sit amet neque rhoncus "
> 				+ "dapibus. Praesent in semper justo, non ornare quam. Donec euismod "
> 				+ "justo ac mi pretium facilisis. Vivamus diam justo, sagittis sit amet "
> 				+ "velit in, dictum bibendum nunc. In quis mollis justo. Fusce et nunc "
> 				+ "vestibulum, consequat massa eget, porttitor massa.");
> 		
> 		//6#4.1
> 		System.out.println("\n Question 6#4.1 \n");
> 		try 
> 		{
> 			commentableText.addComment("Anna", 100, 15);
> 			System.out.println("The Anna's comment has been added to the text.");
> 		} 
> 		catch (TextCommentException exception) 
> 		{
> 			exception.printStackTrace();
> 		}
> 		System.out.println();
> 		try 
> 		{
> 			commentableText.addComment("Bob", 210, 210);
> 			System.out.println("The Bob's comment has been added to the text.");
> 		} 
> 		catch (TextCommentException exception) 
> 		{
> 			exception.printStackTrace();
> 		}
> 		System.out.println();
> 		try 
> 		{
> 			commentableText.addComment("Charles", 6, 10);
> 			System.out.println("The Charles' comment has been added to the text.");
> 		} 
> 		catch (TextCommentException exception) 
> 		{
> 			exception.printStackTrace();
> 		}
> 		System.out.println();
> 		try 
> 		{
> 			commentableText.addComment("", 156, 214);
> 			System.out.println("The anonymous comment has been added to the text.");
> 		} 
> 		catch (TextCommentException exception) 
> 		{
> 			exception.printStackTrace();
> 		}
> 	}
> }
> ```
>
> En essayant les quatre portions de code ci-dessus, on obtient la sortie suivante :

> ```
> 
>  Question 6#4.1 
> 
> et3.java.exceptions.EmptyTextCommentException: The text linked to the comment is empty. The comment cannot be created.
> 	at et3.java.commentable.CommentableImmutableText$TextComment.<init>(CommentableImmutableText.java:36)
> 	at et3.java.commentable.CommentableImmutableText.addComment(CommentableImmutableText.java:124)
> 	at et3.java.application.Main.main(Main.java:23)
> 
> et3.java.exceptions.EmptyTextCommentException: The text linked to the comment is empty. The comment cannot be created.
> 	at et3.java.commentable.CommentableImmutableText$TextComment.<init>(CommentableImmutableText.java:36)
> 	at et3.java.commentable.CommentableImmutableText.addComment(CommentableImmutableText.java:124)
> 	at et3.java.application.Main.main(Main.java:33)
> 
> The Charles' comment has been added to the text.
> 
> et3.java.exceptions.AnonymousCommentException: No author has been registered for this comment. The comment cannot be created.
> 	at et3.java.commentable.CommentableImmutableText$TextComment.<init>(CommentableImmutableText.java:42)
> 	at et3.java.commentable.CommentableImmutableText.addComment(CommentableImmutableText.java:124)
> 	at et3.java.application.Main.main(Main.java:53)
> ```

6#5 Implémentez une stratégie de tri par défaut pertinente en typant votre type TextComment avec l'interface java.lang.Comparable<T>  et en implémentant cette interface.

6#5.1 L'interface java.lang.Comparable<T> est un type paramétré. Qu'est-ce que cela apporte dans ce cas précis relativement à l'ancienne définition non paramétrée de cette interface java.lang.Comparable ?

>
>

6#5.2 Quels sont les avantages et les limitations d'un recours à une définition de la comparaison entre instances interne à une classe telle que permise par l'interface java.lang.Comparable<T> ?

>
>

6#5.3 code

> ```Java
> ```

6#6 Ajoutez dans votre classe CommentableImmutableText une méthode permettant de réaliser l'affichage détaillé des commentaires (dont l'extrait de texte commenté) et prenant comme paramètre une stratégie de tri de commentaires : displayComments(CommentSortStrategy strategy). En testant sur les valeurs possibles de la stratégie de tri, implémentez initialement le tri avec la stratégie par défaut (reposant sur le tri (déjà) implémenté dans la classe TextComment). Assurez-vous que le choix d'une stratégie définie dans l'énumération CommentSortStrategy mais non implémentée concrètement mènera à la levée d'une exception d'un type approprié. Le tri lui-même pourra être effectué à l'aide de la méthode statique java.util.Collections.sort(List<T> list) .

6#6.1 Quel va être l'effet du recours à la méthode sort(List<T> list)sur la liste transmise en paramètre ? On suppose néanmoins que cela peut être souhaitable ici : pourquoi ?Si l'on ne souhaitait pas cet effet, que faudrait-il faire ?

6#6.2 code

> ```Java
> ```

6#7 Implémentez à présent l'ensemble des autres stratégies de tri de commentaires prévues. Pour cela, enrichissez la méthode displayComments en associant chaque valeur de CommentSortStrategy à la définition d'une classe anonyme implémentant java.util.Comparator<E> , et en ayant désormais recours pour le tri lui-même à la méthode statique java.util.Collections.sort(List<T> list, Comparator<? super T> comparator) .

6#7.1 Pourquoi le recours à la définition d'une classe anonyme implémentant java.util.Comparator<E> peut-il être approprié ici ? Quelle pourrait être une alternative à l'utilisation d'une classe anonyme, pour quels avantages et inconvénients ?

>
>

6#7.2 code

> ```Java
> ```

6#8. Testez votre programme sur un jeu de données et pour différentes stratégies de tri des commentaires.

6#8.1 trace d'exécution

> ```
> ```

6#9 On s'intéresse à présent à déterminer la popularité des fragments de texte commentés par les lecteurs. Pour cela, on considérera un fragment de texte comme d'autant plus populaire qu'il a été sélectionné exactement  de nombreuses fois dans des commentaires. Proposez une solution simple  via une méthode de votre classe CommentableImmutableText permettant d'afficher les fragments commentés du texte par popularité décroissante.

6#9.1 code

> ```Java
> ```

6#9.2 trace d'exécution

> ```
> ```
