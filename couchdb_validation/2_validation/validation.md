!SLIDE subsection transition=fade

# Document Validation Functions #

!SLIDE bullets incremental

# Document Validation Functions #

* design document: validate\_doc\_update
* create or update
* raises an exception
* optional

!SLIDE commandline

# Document Validation Functions #

	$ throw({unauthorized : message});

	$ throw({forbidden : message});

!SLIDE

# Document Validation Functions #

	@@@ javaScript

		function(newDoc, oldDoc, userCtx) {}

		function(newDoc, oldDoc, userCtx) {
			throw({forbidden : 'no way'});
		}

!SLIDE small

# Type #

	@@@ javaScript

		function(newDoc, oldDoc, userCtx) {
			if (newDoc.type == "post")
				// validation logic for posts
			if (newDoc.type == "comment")
				// validation logic for comments
			if (newDoc.type == "unicorn")
				// validation logic for unicorns
		}

		function(newDoc, oldDoc, userCtx) {
			if (newDoc.title && newDoc.body)
				// validate that the document has an author
		}


!SLIDE smaller

# Required Fields #

	@@@ javaScript
		function(newDoc, oldDoc, userCtx) {
			function require(field, message) {
				message = message ||
					"Document must have a " + field;
				if (!newDoc[field]) throw({forbidden : message});
			};

			if (newDoc.type == "post") {
				require("title");
				require("created_at");
				require("body");
				require("author");
			}
			if (newDoc.type == "comment") {
				require("name");
				require("created_at");
				require("comment",
					"You may not leave an empty comment");
			}
		}

!SLIDE smaller

# Timestamps #

	@@@ javaScript
		function(newDoc, oldDoc, userCtx) {
			function unchanged(field) {
				if (oldDoc && toJSON(oldDoc[field]) !=
											toJSON(newDoc[field]))
					throw({forbidden : "Field can't be changed: "
						+ field});
			}
			unchanged("created_at");
		}

!SLIDE

# Timestamps #

	@@@ javaScript
		js> [] == []
		false

	distinct objects

!SLIDE bullets incremental

# Timestamps #

* toJSON: convert objects to a string representation
* deeply nested objects: may serialize objects
* Spider-Monkey

!SLIDE smaller

# Authorship #

	@@@ javaScript

		function(newDoc, oldDoc, userCtx) {
			if (newDoc.author) {
				enforce(newDoc.author == userCtx.name,
					"You may only update documents with author "
						+ userCtx.name);
			}
		}